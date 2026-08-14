# Base Task 13: Performance Benchmark Tests (v8)

## Objective
Codify performance expectations as tests that fail if regressions are introduced.

## Why
Without performance baselines, optimizations can silently regress. Benchmark tests enforce response-time, caching-effectiveness, and concurrency-isolation expectations.

## Scope
- **`tests/performance/test_api_response_times.py`**:
  - Health endpoint < 50ms.
  - Hub list < 200ms.
  - Agent CRUD < 300ms.
  - Workflow execution < configurable timeout.
- **`tests/performance/test_caching_effectiveness.py`**:
  - First call (cache miss) vs second call (cache hit) — verify >50% speedup.
  - Cache invalidation — verify stale data not served after mutation.
- **`tests/performance/test_concurrent_access.py`**:
  - 10 concurrent hub creation requests — no race conditions.
  - 10 concurrent workflow runs — proper isolation.
  - 10 concurrent ingestion jobs — no chunk duplication.

## Associated Subtasks
1. `[ ]` `sub_13_01_api_response_times.md`: `test_api_response_times.py` — response-time assertions.
2. `[ ]` `sub_13_02_caching_effectiveness.md`: `test_caching_effectiveness.py` — cache hit speedup & invalidation.
3. `[ ]` `sub_13_03_concurrent_access.md`: `test_concurrent_access.py` — concurrency isolation.

## Definition of Done
- Response-time assertions pass against actual running Gateway (`:8000`) and Postgres (`:5432`) (health < 50ms, hub list < 200ms, agent CRUD < 300ms).
- Cache effectiveness verified on actual Redis (`:6379`) (>50% speedup on hit; stale data not served after mutation).
- Concurrent hub creation, workflow runs, and ingestion jobs show no race conditions or duplication.
- If benchmark failures occur, inspect **Docker container logs** (`docker compose logs db redis qdrant`) and fix root causes directly in backend or submodule code.
- Deleting test data is not necessary.
