# Subtask 05_01: Ingestion Real Integration Tests

## Parent Base Task
`tasks/v8/base/05_syntraflow_ingestion_retrieval_real.md`

## Objective
Create `tests/integration/syntraflow/test_ingestion_real.py` — ingestion pipeline, both embedder paths, local OCR.

## Tasks
1. [ ] Upload PDF/text file → ingestion job created → job completes → chunks stored in Qdrant.
2. [ ] Verify chunk metadata (source, page numbers, timestamps).
3. [ ] Verify collection auto-creation and schema sync.
4. [ ] Multi-file batch ingestion.
5. [ ] Duplicate document detection and deduplication.
6. [ ] **Local embedder path**: Ingest with `harrier-0.6b` (BAAI/bge-base-en-v1.5, 768-dim) → verify Qdrant vectors are 768-dim.
7. [ ] **API embedder path**: Ingest with `gemini/gemini-embedding-2` via LiteLLM → verify vectors stored correctly.
8. [ ] **Local OCR path**: Ingest scanned PDF with `OCR_PROVIDER=local` (Baidu Unlimited-OCR) → verify text extracted.

## Definition of Done
- Ingestion jobs complete and store correctly-dimensioned vectors in real Qdrant for both `harrier-0.6b` (768-dim) and `gemini/gemini-embedding-2` paths.
- Local OCR path extracts text from scanned PDFs.
- Chunk metadata, collection auto-creation, batch ingestion, and deduplication verified.
