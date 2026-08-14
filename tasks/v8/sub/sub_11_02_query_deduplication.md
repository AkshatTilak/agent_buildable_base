# Subtask 11_02: Query Deduplication (N+1 Fixes)

## Parent Base Task
`tasks/v8/base/11_backend_performance_optimizations.md`

## Objective
Identify and fix N+1 query patterns in the backend.

## Tasks
1. [ ] Fix hub member loading (currently individual queries per member).
2. [ ] Fix agent/workflow listing with related data.
3. [ ] Fix eval results with test case details.

## Definition of Done
- N+1 query patterns eliminated for hub members, agent/workflow listing, and eval results against actual running Postgres (`:5432`).
- If query errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
