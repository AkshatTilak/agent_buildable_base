# Subtask 13_02: Caching Effectiveness Benchmarks

## Parent Base Task
`tasks/v8/base/13_performance_benchmark_tests.md`

## Objective
Create `tests/performance/test_caching_effectiveness.py` — cache hit speedup & invalidation.

## Tasks
1. [ ] First call (cache miss) vs second call (cache hit) — verify >50% speedup.
2. [ ] Cache invalidation — verify stale data not served after mutation.

## Definition of Done
- Cache effectiveness verified on actual Redis (`:6379`) (>50% speedup on hit).
- Stale data not served after mutation.
- If errors arise, inspect Docker container logs (`docker compose logs redis db`) and fix root causes directly.
- Deleting test data is not necessary.
