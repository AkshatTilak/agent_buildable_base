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
  - **Local embedder path**: Ingest with `harrier-0.6b` (BAAI/bge-base-en-v1.5, 768-dim) → verify Qdrant vectors are 768-dim.
  - **API embedder path**: Ingest with `gemini/gemini-embedding-2` via LiteLLM → verify vectors stored correctly.
  - **Local OCR path**: Ingest scanned PDF with `OCR_PROVIDER=local` (Baidu Unlimited-OCR) → verify text extracted.
- **`tests/integration/syntraflow/test_retrieval_real.py`** — Retrieval engine:
  - Ingest known documents → query with known terms → verify relevant chunks returned.
  - Vector-only retrieval vs hybrid (vector + graph) retrieval comparison.
  - Metadata filtering (by source, date, collection).
  - Hub-scoped retrieval (only returns docs from bound collections).
  - Test retrieval with both `jina-clip-v2` (1024-dim) and `harrier-0.6b` (768-dim) collections.
- **`tests/integration/syntraflow/test_collections_real.py`** — Collection management:
  - Create/list/delete collections on real Qdrant.
  - Collection schema sync between Postgres and Qdrant.
  - Collection access control via hub bindings.

## Associated Subtasks
1. `[ ]` `sub_05_01_ingestion_real.md`: `test_ingestion_real.py` — ingestion pipeline, both embedder paths, local OCR.
2. `[ ]` `sub_05_02_retrieval_real.md`: `test_retrieval_real.py` — retrieval engine, hybrid, metadata filtering, hub scoping.
3. `[ ]` `sub_05_03_collections_real.md`: `test_collections_real.py` — collection management & schema sync.

## Definition of Done
- Ingestion jobs complete and store correctly-dimensioned vectors in real Qdrant for both `harrier-0.6b` (768-dim) and `gemini/gemini-embedding-2` paths.
- Local OCR path extracts text from scanned PDFs.
- Retrieval returns relevant chunks with metadata filtering and hub scoping.
- Collection create/list/delete and schema sync verified against real Qdrant.
