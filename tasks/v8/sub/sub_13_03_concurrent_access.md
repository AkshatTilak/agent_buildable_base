# Subtask 13_03: Concurrent Access Benchmarks

## Parent Base Task
`tasks/v8/base/13_performance_benchmark_tests.md`

## Objective
Create `tests/performance/test_concurrent_access.py` — concurrency isolation.

## Tasks
1. [ ] 10 concurrent hub creation requests — no race conditions.
2. [ ] 10 concurrent workflow runs — proper isolation.
3. [ ] 10 concurrent ingestion jobs — no chunk duplication.

## Definition of Done
- Concurrent hub creation, workflow runs, and ingestion jobs show no race conditions or duplication against actual running dev services.
- If concurrency errors arise, inspect Docker container logs (`docker compose logs db redis qdrant`) and fix root causes directly.
- Deleting test data is not necessary.
