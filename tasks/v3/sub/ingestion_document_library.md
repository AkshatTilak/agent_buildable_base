# Subtask: Document Library View Component

**Parent Link:** `base/06_ingestion_documents_ux.md`

## Actionable Steps
- [x] Create `frontend/src/components/ingestion/DocumentLibrary.tsx`.
- [x] Render a table/grid of all ingested documents retrieved from `GET /api/syntraflow/documents`.
- [x] Table columns: File Name, File Type, File Hash (shortened mono font), Total Chunks, Ingested Date, Actions (Delete / View Chunks).
- [x] Add search input to filter documents by file name.
- [x] Add pagination controls (Previous / Next / Page Size).
- [x] Add Document Chunks Viewer Modal — click "View Chunks" to open modal showing chunk text snippets and embeddings status.
- [x] Add Delete Document Action — triggers `DELETE /api/syntraflow/documents/{id}` with `ConfirmModal`.

## Dependencies
- `sub/fix_backend_documents_jobs_api.md` (for document endpoints), `sub/design_shared_components.md` (ConfirmModal).

## Definition of Done
- Document library lists ingested documents with chunk counts.
- Search and pagination work smoothly.
- Viewing document chunks in modal works.
- Document deletion works with confirmation dialog.
