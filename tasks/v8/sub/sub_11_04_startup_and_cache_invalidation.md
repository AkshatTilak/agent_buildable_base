# Subtask 11_04: Startup Optimization & Cache Invalidation

## Parent Base Task
`tasks/v8/base/11_backend_performance_optimizations.md`

## Objective
Lazy-load submodule routes, tune connection pools, and implement proper cache invalidation on mutations.

## Tasks
1. [x] Lazy-load submodule routes (defer import until route is first hit).
2. [x] Connection pool tuning (Postgres pool size, Qdrant connection limits).
3. [x] Cache invalidation on mutations:
   - Hub update/delete → clear hub cache.
   - Member add/remove → clear membership cache.
   - Collection create/delete → clear collection cache.

## Definition of Done
- Submodule routes lazy-loaded; connection pools tuned.
- Cache invalidation on hub/member/collection mutations verified on actual Redis (`:6379`) (no stale data served).
- If errors arise, inspect Docker container logs (`docker compose logs redis db`) and fix root causes directly.
- Deleting test data is not necessary.
