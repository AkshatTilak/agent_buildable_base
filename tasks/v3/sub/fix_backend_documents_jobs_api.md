# Subtask: Add Documents Listing & Jobs Listing Backend Endpoints

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [ ] `projects/syntraflow/api.py` — Add `GET /documents` endpoint:
  - Query `SyntraFlowDocument` table with pagination (offset/limit query params).
  - Return list with fields: `id`, `filename`, `file_hash`, `file_type`, `chunk_count`, `created_at`.
  - Include `total_count` for frontend pagination.
- [ ] `projects/syntraflow/api.py` — Add `GET /jobs` endpoint:
  - Query `SyntraFlowJob` table with optional filters: `status` (queued/processing/completed/failed), `limit`, `offset`.
  - Return list with fields: `job_id`, `document_id`, `status`, `progress`, `error_msg`, `created_at`, `updated_at`.
  - Order by `created_at DESC`.
- [ ] Add `GET /documents/{doc_id}/chunks` endpoint for viewing chunk details of a specific document.
- [ ] Update `frontend/src/services/api.ts` — Add `getDocuments()`, `getJobs()`, `getDocumentChunks()` methods.

## Dependencies
- Database models (`SyntraFlowDocument`, `SyntraFlowJob`) must exist (they already do from v2).

## Definition of Done
- `GET /api/syntraflow/documents` returns paginated document list.
- `GET /api/syntraflow/jobs` returns paginated job list with filter support.
- Frontend API client has corresponding methods.
