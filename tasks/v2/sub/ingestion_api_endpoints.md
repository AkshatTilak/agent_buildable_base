# Subtask: Ingestion API Endpoints

**Parent Link:** `base/03_ingestion_pipeline_strats.md`

## Actionable Steps
- [x] Update SyntraFlow API endpoints in `projects/syntraflow/api.py`.
- [x] Modify the ingest endpoints to accept optional parameters:
  - [x] `chunker_type` (fixed, recursive, semantic) and its parameters (e.g. `chunk_size`, `overlap`).
  - [x] `pre_processors` (list of names).
  - [x] `post_processors` (list of names).
- [x] Inside `ingest_document_pipeline`, dynamically instantiate the strategy classes based on API inputs and orchestrate their sequential execution.

## Dependencies
- `sub/ingestion_chunking_impl.md`
- `sub/ingestion_processing_impl.md`

## Definition of Done
- Endpoint accepts JSON configuration payloads.
- Automated API test uploads a document with complex chunker options and verifies database rows (chunks/documents) are written correctly.
