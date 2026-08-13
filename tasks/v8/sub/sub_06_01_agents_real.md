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
- Agent CRUD and hub scoping verified against real Postgres.
- Endpoint slug regeneration rules verified on update.
