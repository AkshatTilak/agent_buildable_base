# Subtask 07_03: Eval Hub Real Integration Tests

## Parent Base Task
`tasks/v8/base/07_evalops_evaluation_real.md`

## Objective
Create `tests/integration/evalops/test_eval_hub_real.py` — eval hub management & dashboard metrics.

## Tasks
1. [x] Eval hub CRUD.
2. [x] Link eval hub to agent hub → run evals against agent's model.
3. [x] Eval dashboard metrics aggregation.

## Definition of Done
- Eval hub CRUD, agent-hub linking, and dashboard metrics aggregation verified against actual running Postgres (`:5432`).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend eval hub code.
- Deleting test data is not necessary.
