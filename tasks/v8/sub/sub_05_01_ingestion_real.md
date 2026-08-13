# Subtask 05_01: Ingestion Real Integration Tests

## Parent Base Task
`tasks/v8/base/05_syntraflow_ingestion_retrieval_real.md`

## Objective
Create `tests/integration/syntraflow/test_ingestion_real.py` — ingestion pipeline, both embedder paths, local OCR.

## Tasks
1. [x] Upload PDF/text file → ingestion job created → job completes → chunks stored in Qdrant.
2. [x] Verify chunk metadata (source, page numbers, timestamps).
3. [x] Verify collection auto-creation and schema sync.
4. [x] Multi-file batch ingestion.
5. [x] Duplicate document detection and deduplication.
6. [x] **Local embedder path**: Ingest with compatibility alias `harrier-0.6b`, resolved to `microsoft/harrier-oss-v1-0.6b` (1,024-dim, 32,768-token max) → verify Qdrant vectors are 1,024-dim.
7. [ ] **API embedder path**: Ingest with `gemini/gemini-embedding-2` via LiteLLM → verify vectors stored correctly. **BLOCKED** — inference server `/infer/embed` has no loader for API embedders (see `references/issues/08_05_syntraflow_real_bugs_and_blockers.md`).
8. [ ] **Local OCR path**: Ingest scanned PDF with `OCR_PROVIDER=local` (Baidu Unlimited-OCR) → verify text extracted. **BLOCKED** — OCR model not configured/loadable on CPU-only host (see `references/issues/08_05_syntraflow_real_bugs_and_blockers.md`).

## Definition of Done
- Ingestion jobs complete and store correctly-dimensioned vectors in real Qdrant for both `microsoft/harrier-oss-v1-0.6b` (1,024-dim) and `gemini/gemini-embedding-2` paths.
- Local OCR path extracts text from scanned PDFs.
- Chunk metadata, collection auto-creation, batch ingestion, and deduplication verified.

## Status
- `test_ingestion_real.py` created; **4 tests pass** against real Postgres & Qdrant.
- Local embedder (Harrier OSS v1 0.6B, 1,024-dim), PDF/text ingestion, batch, and dedup verified.
- API embedder and OCR paths blocked by infrastructure (see issue doc).
