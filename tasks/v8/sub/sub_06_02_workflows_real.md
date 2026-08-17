# Subtask 06_02: Workflows Real Integration Tests

## Parent Base Task
`tasks/v8/base/06_guardroute_workflows_agents_real.md`

## Objective
Create `tests/integration/guardroute/test_workflows_real.py` — workflow lifecycle & versioning.

## Tasks
1. [x] Create workflow with canvas JSON → verify DB storage.
2. [x] Workflow versioning (save new version → list versions → restore old version).
3. [x] Workflow import/export (portability).
4. [x] Workflow-hub scoping.

## Definition of Done
- Workflow lifecycle, versioning, import/export, and hub scoping verified against actual running Postgres (`:5432`) and Gateway (`:8000`).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend workflow code or GuardRoute submodule.
- Deleting test data is not necessary.
