# SyntraFlow — Ingestion & Hybrid RAG Architecture

> **Source:** Migrated from `requirements/syntraflow.md`
> **Last Updated:** 2026-07-14

SyntraFlow handles ingestion, layout-aware OCR extraction, keyframe/ASR audio alignment, index writing (PostgreSQL, Qdrant, Neo4j), and MCP retrieval server. Model selection is configurable via the Model Registry (see `references/logic/model_registry.md`).

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
- Check against `syntraflow_documents` table.
- Duplicate → return existing document ID; skip re-processing.

### Ingestion Job Status Tracking
```sql
CREATE TABLE syntraflow_jobs (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id UUID REFERENCES syntraflow_documents(id),
    status      VARCHAR(20) NOT NULL DEFAULT 'queued',  -- queued, processing, completed, failed
    progress    FLOAT DEFAULT 0.0,
    error_msg   TEXT,
    created_at  TIMESTAMP DEFAULT NOW(),
    updated_at  TIMESTAMP DEFAULT NOW()
);
```
- Publish to Kafka topic `syntraflow-ingestion-jobs` for async processing.
- Status endpoint: `GET /api/syntraflow/jobs/{job_id}`.

---

## 4. Database Writing & KG Population ✅

### Relational DB (PostgreSQL)
- Document metadata, aligned video segments, chunk payloads to Postgres.
- Prefix: `syntraflow_` for all tables (`syntraflow_documents`, `syntraflow_chunks`, `syntraflow_video_segments`).

### Vector Indexing (Qdrant)
- Embeddings via configured model (default: jina-clip-v2).
- Collection: `syntraflow_chunks_v1`.
- Vector dimension: determined by active embedding model (default: 1024).
- Batch upserts: 100 vectors per upsert call.

### Knowledge Graph (Neo4j)
- LLM extraction pass over text chunks → Entities + Relationships.
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
| `retrieve_documents` | `(query, strategy, limit) -> str` | Hybrid retrieval: Vector + Graph + RRF |
| `retrieve_video_segments` | `(query, limit) -> str` | Semantic search against video timeline blocks |
| `query_database` | `(table, filters, columns) -> str` | Parameterized DB helper; blocks non-`syntraflow_` tables |
| `query_graph` | `(cypher_query) -> str` | Read-only Cypher; blocks write operations |

---

## 6. Document Deletion & Cleanup ✅
`DELETE /api/syntraflow/documents/{doc_id}` — Cascade delete:
- Remove vectors from Qdrant by document ID filter.
- Remove graph nodes/edges from Neo4j by document reference.
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
