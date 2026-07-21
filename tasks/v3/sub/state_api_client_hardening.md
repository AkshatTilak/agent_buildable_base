# Subtask: API Client Hardening

**Parent Link:** `base/05_frontend_state_data_layer.md`

## Actionable Steps
- [x] Replace all `request<any>` and `request<any[]>` calls in `api.ts` with proper typed generics from `types/api.ts`.
- [x] Replace hardcoded `API_BASE_URL` and `"sk_live_default_key"` — read from `useStore.getState()` settings.
- [x] Add request retry logic with exponential backoff (max 3 retries, 1s→2s→4s).
- [x] Add response error interceptor that triggers toast notification on failure.
- [x] Add new API methods:
  - `getDocuments(offset, limit)` → `GET /api/syntraflow/documents`
  - `getJobs(status?, offset, limit)` → `GET /api/syntraflow/jobs`
  - `getJobStatus(jobId)` → `GET /api/syntraflow/jobs/{id}`
  - `deleteDocument(docId)` → `DELETE /api/syntraflow/documents/{id}`
- [x] Add request timeout: 30 seconds via `AbortController`.
- [x] Add `Content-Type` header only for JSON bodies (not FormData uploads).

## Dependencies
- `sub/state_typescript_interfaces.md`, `sub/state_new_zustand_slices.md` (settingsSlice for URL/key).

## Definition of Done
- Zero `any` types in `api.ts`.
- API client reads URL/key from settings store.
- Retry logic handles transient failures.
- New document/job endpoints are available.
