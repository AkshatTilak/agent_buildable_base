# Base Task 5: Real-World Integration Tests — SyntraFlow (Ingestion & Retrieval) (v8)

## Objective
Test document ingestion pipelines, vector storage, and retrieval against real Qdrant and Neo4j, covering both local and API embedder paths plus the local OCR path.

## Why
Ingestion and retrieval are the heart of the RAG system. Existing tests mock Qdrant/Neo4j entirely, so vector dimension mismatches, collection schema drift, and retrieval correctness are never validated in production-like conditions.

## Scope
- **`tests/integration/syntraflow/test_ingestion_real.py`** — Ingestion pipeline:
  - Upload PDF/text file → ingestion job created → job completes → chunks stored in Qdrant.
  - Verify chunk metadata (source, page numbers, timestamps).
  - Verify collection auto-creation and schema sync.
  - Multi-file batch ingestion.
  - Duplicate document detection and deduplication.
  - **Local embedder path**: Ingest with compatibility alias `harrier-0.6b`, resolved to `microsoft/harrier-oss-v1-0.6b` (1,024-dim, 32,768-token max) → verify Qdrant vectors are 1,024-dim.
  - **API embedder path**: Ingest with `gemini/gemini-embedding-2` via LiteLLM → verify vectors stored correctly.
  - **Local OCR path**: Ingest scanned PDF with `OCR_PROVIDER=local` (Baidu Unlimited-OCR) → verify text extracted.
- **`tests/integration/syntraflow/test_retrieval_real.py`** — Retrieval engine:
  - Ingest known documents → query with known terms → verify relevant chunks returned.
  - Vector-only retrieval vs hybrid (vector + graph) retrieval comparison.
  - Metadata filtering (by source, date, collection).
  - Hub-scoped retrieval (only returns docs from bound collections).
  - Test retrieval with `jina-clip-v2` (1,024-dim), Harrier 0.6B (1,024-dim), and Harrier 270M (640-dim) collections.
- **`tests/integration/syntraflow/test_collections_real.py`** — Collection management:
  - Create/list/delete collections on real Qdrant.
  - Collection schema sync between Postgres and Qdrant.
  - Collection access control via hub bindings.

## Associated Subtasks
1. `[x]` `sub_05_01_ingestion_real.md`: `test_ingestion_real.py` — ingestion pipeline, both embedder paths, local OCR. *(API embedder + OCR paths blocked by infra — see `references/issues/08_05_syntraflow_real_bugs_and_blockers.md`)*
2. `[x]` `sub_05_02_retrieval_real.md`: `test_retrieval_real.py` — retrieval engine, hybrid, metadata filtering, hub scoping.
3. `[x]` `sub_05_03_collections_real.md`: `test_collections_real.py` — collection management & schema sync.

## Definition of Done
- Ingestion jobs complete and store correctly-dimensioned vectors in actual running Qdrant for Harrier 0.6B (1,024-dim), Harrier 270M (640-dim), and `gemini/gemini-embedding-2` paths.
- Local OCR path extracts text from scanned PDFs.
- Retrieval returns relevant chunks with metadata filtering and hub scoping.
- Collection create/list/delete and schema sync verified against actual running Qdrant.
- Deleting test data is not necessary.
- When errors or failures occur, inspect **Docker container logs** (`docker compose logs qdrant neo4j db`) and **fix the underlying backend, inference, or SyntraFlow submodule code** to achieve end-to-end correctness.

## Status
- **12 tests pass** (`tests/integration/syntraflow`): 3 collections + 4 ingestion + 5 retrieval.
- Local embedder (Harrier OSS v1 0.6B, 1,024-dim), PDF/text ingestion, batch, dedup, retrieval (vector/hybrid/metadata/hub-scope/multi-dim), and collection CRUD/schema-sync verified against actual running Postgres, Qdrant & Neo4j.
- Ongoing/blocked paths (such as API embedder routing in inference or local OCR setup) must be diagnosed and resolved in the backend/submodule rather than permanently bypassed.
