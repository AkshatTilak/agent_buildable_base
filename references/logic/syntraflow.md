# SyntraFlow — Ingestion & Hybrid RAG Architecture

> **V6 Update — Hub Scoping.** SyntraFlow is now the **Ingestion Hub**: a deployment holds many ingestion hubs, each owning its own collections, documents, jobs and the physical datastores that back them.
> **V7 Update — Datastore Validation & Harrier 0.6B Support.** Strict datastore binding validation is enforced before collection creation; Harrier 0.6B local embedding support added; stage-by-stage trace logging enabled in `syntraflow.log`.
> All resources described below are scoped by `hub_id`. See
> [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md)
> for the canonical tenancy model.

> **Source:** Migrated from `requirements/syntraflow.md`
> **Last Updated:** 2026-08-05 (V7 platform stabilization)

SyntraFlow handles ingestion, layout-aware OCR extraction, keyframe/ASR audio alignment, index writing (PostgreSQL, Qdrant, Neo4j), and MCP retrieval server. Model selection is configurable via the Model Registry (see `references/logic/model_registry.md`).

Every ingestion resource belongs to exactly one `ingestion` hub. Agents and workflows that live in other hubs may only read a hub's collections through an explicit `hub_link` (see [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.3); there is no global collection plane.

---

## 1. Document Ingestion ✅

### Configurable OCR Engine (via Model Registry)
- **Local Mode:** Load configured local OCR model (default: GLM-OCR) into inference server VRAM on request. Extract raw document structures. Optionally pass to LLM for layout-preserving Markdown.
- **API Mode:** Pass document images to cloud OCR model (default: Gemini 3.5 Flash) for layout-preserving Markdown and tabular JSON. Zero local VRAM cost.
- See `references/logic/model_registry.md` §2 for all OCR model options.

### Layout-Aware Chunking Engine
- Split by logical layout boundaries (headers, paragraphs, sections) — not arbitrary token lengths.
- Maintain parent-child mappings for headers.
- Parameters:
  - `CHUNK_MAX_TOKENS=512`
  - `CHUNK_OVERLAP=50`
  - Minimum chunk size: 50 tokens.

---

## 2. Video & Audio Ingestion ✅

### Asynchronous Stream Demuxing
- Extract audio from video containers (`.mp4`, `.mov`, `.webm`, `.mkv`) using async media pipeline.
- Generate temporary WAV audio files for transcription.
- Uses `ffmpeg-python` or `pydub` (NOT `aiortc`). Requires `ffmpeg` binary in Docker image.

### Speech-to-Text Transcription
- Pass audio to configured ASR model (default: SenseVoice-Small).
- Returns word segments with start/end timestamps.
- Captures emotion markers and audio event tags (unique to SenseVoice).

### Keyframe Scene-Change Sampling
- Sample video keyframes on scene changes (pixel delta thresholds or SSIM).
- Pass keyframes to cloud LLM for visual summaries.

### Temporal Aligner
- Align ASR transcript timestamps with keyframe visual descriptions chronologically.
- Store aligned timeline blocks in relational database.

---

## 3. File Upload & Processing ✅

### File Size & Format Limits
| Category | Max Size | Supported Formats |
|---|---|---|
| Documents | 100 MB | `.pdf`, `.png`, `.jpg`, `.jpeg`, `.tiff`, `.bmp`, `.docx`, `.pptx` |
| Video | 500 MB | `.mp4`, `.mov`, `.webm`, `.mkv` |
| Audio | 200 MB | `.wav`, `.mp3`, `.flac`, `.ogg` |

### Duplicate Document Detection
- SHA-256 hash of file content on upload.
- Check against `syntraflow_documents` table, **scoped to the current `hub_id`** — the same file may be ingested independently into two different ingestion hubs.
- Duplicate → return existing document ID; skip re-processing.

### Ingestion Job Status Tracking
```sql
CREATE TABLE syntraflow_jobs (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    hub_id      VARCHAR(36) NOT NULL REFERENCES hubs(id) ON DELETE CASCADE,  -- V6
    document_id UUID REFERENCES syntraflow_documents(id),
    status      VARCHAR(20) NOT NULL DEFAULT 'queued',  -- queued, processing, completed, failed
    progress    FLOAT DEFAULT 0.0,
    error_msg   TEXT,
    created_at  TIMESTAMP DEFAULT NOW(),
    updated_at  TIMESTAMP DEFAULT NOW()
);
```
- Publish to Kafka topic `syntraflow-ingestion-jobs` for async processing. The job message carries `hub_id` so the worker resolves the correct datastore bindings (§8).
- Status endpoint: `GET /hubs/{hub_id}/jobs/{job_id}`. Job listing and status are always filtered by `hub_id`; a bare job id never resolves.

---

## 4. Global Data Store & KG Population ✅

### Relational DB (PostgreSQL)
- Document metadata, aligned video segments, and chunk payloads to Postgres.
- **Dynamic Collections Table:** `syntraflow_collections` manages multiple Qdrant collections. Tracks collection `id`, `hub_id`, `name`, `physical_name`, `embedding_model`, `dimension`, and `strategy_config_json`.
- **V6 tenancy:** `syntraflow_collections`, `syntraflow_documents`, `syntraflow_chunks`, `syntraflow_video_segments` and `syntraflow_jobs` all carry a **`NOT NULL hub_id`** (denormalised onto documents, chunks and segments for query performance). The V5 soft `tenant_id` column is **dropped**.
- **Uniqueness:** collection names are unique **per hub** — `UNIQUE (hub_id, name)`, no longer globally unique.
- Prefix: `syntraflow_` for all tables (`syntraflow_collections`, `syntraflow_documents`, `syntraflow_chunks`, `syntraflow_video_segments`).

### Vector Indexing (Qdrant Multi-Collection)
- Embeddings via configured model (default: jina-clip-v2).
- **Dynamic Collections:** Collections are created dynamically **per ingestion hub**, registered in the `syntraflow_collections` table, rather than a single static collection.
- **Physical naming:** the friendly per-hub `name` maps to a globally unique physical Qdrant collection `{hub_slug}__{collection_name}`, recorded in the new `physical_name` column. All vector-store calls use `physical_name`; the API only ever exposes `name`.
- **Standard Metadata Payload:** Every vector must include standard filtering metadata: `document_id`, `hub_id`, `collection_id`, `tags`, `timestamp`, `access_level`. (`tenant_id` is gone — `hub_id` replaces it.)
- Batch upserts: 100 vectors per upsert call.
- The target Qdrant instance is resolved per hub with `resolve_vector_client(hub_id)` (§8), never from a single global client.

### Retrieval Strategy Engine
- Every query filters by `hub_id` first; a caller from another hub reaches these collections only through an explicit `hub_link` resolved by `common/services/hub_resolver.py`, re-checked at execution time.
- **Per-collection strategy config:** each collection stores its own default strategy in `strategy_config_json` (strategy, top-k, RRF weights, graph depth). Workflow nodes and agents may override it per call.
- Pluggable retrieval options configured dynamically by workflow nodes or agents:
  - **Dense Search:** Standard cosine similarity via embeddings.
  - **Sparse Search (BM25):** Keyword-based sparse vector search.
  - **Hybrid Search:** Combines Dense and Sparse using Reciprocal Rank Fusion (RRF).
  - **Graph Search:** Neo4j traversal.
- **Multi-collection fan-in:** one search may target several collections **within the same hub**; results are merged with RRF and each hit carries its originating `collection_id`. Fan-in never crosses a hub boundary.

### Knowledge Graph (Neo4j)
- LLM extraction pass over text chunks → Entities + Relationships.
- Written to the Neo4j instance bound to the hub via `resolve_graph_client(hub_id)` (§8); every node and relationship carries its originating `hub_id` so a shared instance stays partitioned.
- **Entity Types:** Person, Organization, Location, Concept, Event, Product.
- Output schema per chunk:
  ```json
  {
    "entities": [{"name": "...", "type": "Person|Organization|...", "description": "..."}],
    "relationships": [{"source": "...", "target": "...", "type": "WORKS_AT|LOCATED_IN|...", "description": "..."}]
  }
  ```
- Write to Neo4j with prefix `SyntraFlow_` for labels.
- Batch processing: extract per chunk, deduplicate, batch write.

---

## 5. MCP (Model Context Protocol) Server ✅

### Transport
- **SSE** transport (compatible with web clients and LangGraph).
- Alternative: **streamable-HTTP** for simpler integrations.

### Tools
| Tool | Signature | Purpose |
|---|---|---|
| `retrieve_documents` | `(hub_id, query, strategy, limit) -> str` | Hybrid retrieval: Vector + Graph + RRF |
| `retrieve_video_segments` | `(hub_id, query, limit) -> str` | Semantic search against video timeline blocks |
| `query_database` | `(hub_id, table, filters, columns) -> str` | Parameterized DB helper; blocks non-`syntraflow_` tables and always injects a `hub_id` predicate |
| `query_graph` | `(hub_id, cypher_query) -> str` | Read-only Cypher; blocks write operations and constrains matches to the hub |

Every tool call is bound to a single ingestion hub. When the caller belongs to a different hub, the MCP server asserts an active `hub_link` before serving the request and fails with `HUB_LINK_REVOKED` otherwise.

---

## 6. Document Deletion & Cleanup ✅
`DELETE /hubs/{hub_id}/documents/{doc_id}` — the document is resolved by `(hub_id, doc_id)`; a bare document id never resolves. Cascade delete:
- Remove vectors from the hub's bound Qdrant instance, from the collection's `physical_name`, by document ID filter.
- Remove graph nodes/edges from the hub's bound Neo4j instance by document reference.
- Remove rows from Postgres tables.
- Return confirmation with counts.

---

## 7. Dependencies (`pyproject.toml` syntraflow extras)
```toml
syntraflow = [
    "langchain (>=0.3.0,<1.0.0)",
    "langgraph (>=0.2.0,<1.0.0)",
    "sentence-transformers (>=3.0.0,<4.0.0)",
    "mcp (>=0.1.0,<1.0.0)",
    "neo4j (>=5.20.0,<6.0.0)",
    "confluent-kafka (>=2.4.0,<3.0.0)",
    "ffmpeg-python (>=0.2.0,<1.0.0)",
    "pydub (>=0.25.1,<1.0.0)",
]
```
Remove: `aiortc` (WebRTC — incorrect), `mlflow` (move to evalops if needed).

---

## 8. Hub Datastore Bindings (V6)

An ingestion hub manages not only its collections but **the databases that store them**. Bindings live in
`datastore_bindings`; the schema is defined in
[`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.4.

- A hub may bind its own **Qdrant**, **Neo4j**, **Postgres** and **OpenSearch** instances (`store_type`).
- Connection credentials are **Fernet-encrypted at rest** with `DATASTORE_ENCRYPTION_KEY` and are **never** returned by the API; responses expose a masked `connection_uri` only.
- Exactly **one default per `(hub_id, store_type)`** (`is_default = true`). If a hub declares no binding of a type, the platform default from `common/config/settings.py` is used and surfaced read-only as a synthetic *Platform Default* binding.
- Binding names are unique per hub — `UNIQUE (hub_id, name)`.
- **Background health checks** probe each binding on a schedule and update `health_status` / `last_health_check`. An unhealthy default blocks new ingestion jobs with a descriptive error rather than silently falling back.
- Managing bindings requires hub role `maintainer` or above.

### Client resolution

```python
# projects/syntraflow/src/datastores/resolver.py
async def resolve_vector_client(hub_id: str) -> QdrantClient: ...
async def resolve_graph_client(hub_id: str) -> Neo4jClient: ...
```

Both resolve the hub's default binding for the store type, decrypt its credentials, and return a cached
client, falling back to the platform default when no binding exists. **No ingestion, indexing, or
retrieval path may construct a datastore client directly** — every path goes through these resolvers.

---

## 9. V6 Route Surface

All SyntraFlow routes are nested under an ingestion hub and guarded by
`require_hub(hub_type='ingestion', ...)`. The flat `/api/syntraflow/*` routes are **removed** — no
back-compat aliases are retained.

| Method | Path | Min role | Purpose |
|---|---|---|---|
| `GET` / `POST` | `/hubs/{hub_id}/collections` | viewer / contributor | List, create collections (`UNIQUE (hub_id, name)`) |
| `GET` / `PATCH` | `/hubs/{hub_id}/collections/{collection_id}` | viewer / contributor | Inspect, update strategy config |
| `DELETE` | `/hubs/{hub_id}/collections/{collection_id}` | maintainer | Drop collection and its physical Qdrant collection |
| `GET` / `POST` | `/hubs/{hub_id}/documents` | viewer / contributor | List, upload documents |
| `DELETE` | `/hubs/{hub_id}/documents/{doc_id}` | maintainer | Cascade delete (§6) |
| `GET` | `/hubs/{hub_id}/jobs` | viewer | Ingestion job list |
| `GET` | `/hubs/{hub_id}/jobs/{job_id}` | viewer | Job status |
| `GET` / `POST` | `/hubs/{hub_id}/datastores` | viewer / maintainer | List, create datastore bindings (§8) |
| `PATCH` / `DELETE` | `/hubs/{hub_id}/datastores/{binding_id}` | maintainer | Update, remove binding |
| `POST` | `/hubs/{hub_id}/datastores/{binding_id}/test` | maintainer | On-demand health check |
| `POST` | `/hubs/{hub_id}/search` | viewer | Retrieval (dense / sparse / hybrid / graph, multi-collection fan-in) |

Cross-hub consumers (agents, workflow retrieval nodes) call the same routes; access is granted only by an
`agent → ingestion` or `workflow → ingestion` `hub_link` and is re-validated at execution time.
