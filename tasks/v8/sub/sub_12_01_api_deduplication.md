# Subtask 12_01: API Call Deduplication

## Parent Base Task
`tasks/v8/base/12_frontend_performance_optimizations.md`

## Objective
Review `frontend/src/services/api.ts` for duplicate fetches and add React Query deduplication, optimistic updates, and request batching.

## Tasks
1. [ ] Use React Query's built-in deduplication (`useQuery` with `staleTime`).
2. [ ] Add `staleTime` and `gcTime` to all query hooks.
3. [ ] Implement optimistic updates for hub/agent/workflow CRUD.
4. [ ] Batch related API calls on page load (e.g., hub metadata + members + links in one request, or parallel `Promise.all`).

## Definition of Done
- All query hooks use `staleTime`/`gcTime`.
- Optimistic updates for CRUD.
- Related API calls batched on page load.
