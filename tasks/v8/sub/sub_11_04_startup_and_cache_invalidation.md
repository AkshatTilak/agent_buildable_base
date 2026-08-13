# Subtask 11_04: Startup Optimization & Cache Invalidation

## Parent Base Task
`tasks/v8/base/11_backend_performance_optimizations.md`

## Objective
Lazy-load submodule routes, tune connection pools, and implement proper cache invalidation on mutations.

## Tasks
1. [ ] Lazy-load submodule routes (defer import until route is first hit).
2. [ ] Connection pool tuning (Postgres pool size, Qdrant connection limits).
3. [ ] Cache invalidation on mutations:
   - Hub update/delete → clear hub cache.
   - Member add/remove → clear membership cache.
   - Collection create/delete → clear collection cache.

## Definition of Done
- Submodule routes lazy-loaded; connection pools tuned.
- Cache invalidation on hub/member/collection mutations verified (no stale data served).
