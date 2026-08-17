# Subtask 07_01: Datasets Real Integration Tests

## Parent Base Task
`tasks/v8/base/07_evalops_evaluation_real.md`

## Objective
Create `tests/integration/evalops/test_datasets_real.py` — dataset management & import.

## Tasks
1. [x] Create eval dataset → add test cases → list/get/delete.
2. [x] Dataset import from JSON fixtures.
3. [x] Dataset-hub scoping.

## Definition of Done
- Dataset create/list/get/delete and JSON import verified against actual running Postgres (`:5432`).
- Dataset-hub scoping verified.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend dataset services.
- Deleting test data is not necessary.
