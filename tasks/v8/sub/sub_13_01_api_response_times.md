# Subtask 13_01: API Response Time Benchmarks

## Parent Base Task
`tasks/v8/base/13_performance_benchmark_tests.md`

## Objective
Create `tests/performance/test_api_response_times.py` — response-time assertions.

## Tasks
1. [ ] Health endpoint < 50ms.
2. [ ] Hub list < 200ms.
3. [ ] Agent CRUD < 300ms.
4. [ ] Workflow execution < configurable timeout.

## Definition of Done
- Response-time assertions pass against actual running Gateway (`:8000`) and Postgres (`:5432`) (health < 50ms, hub list < 200ms, agent CRUD < 300ms).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
