# Subtask 06_01: Agents Real Integration Tests

## Parent Base Task
`tasks/v8/base/06_guardroute_workflows_agents_real.md`

## Objective
Create `tests/integration/guardroute/test_agents_real.py` — agent CRUD & hub scoping.

## Tasks
1. [ ] Create agent with system prompt + model config → verify DB row.
2. [ ] Update agent → verify endpoint slug regeneration rules.
3. [ ] Delete agent → verify cleanup.
4. [ ] Agent-hub scoping (agent only accessible within its hub).

## Definition of Done
- Agent CRUD and hub scoping verified against actual running Postgres (`:5432`) and Gateway (`:8000`).
- Endpoint slug regeneration rules verified on update.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend agent services or GuardRoute code.
- Deleting test data is not necessary.
