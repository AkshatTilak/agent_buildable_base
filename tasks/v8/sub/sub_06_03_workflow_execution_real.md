# Subtask 06_03: Workflow Execution Real Integration Tests

## Parent Base Task
`tasks/v8/base/06_guardroute_workflows_agents_real.md`

## Objective
Create `tests/integration/guardroute/test_workflow_execution_real.py` — workflow runs with real LLM.

## Tasks
1. [ ] Execute a simple linear workflow (input → LLM → output) using `gemini/gemini-3.5-flash` via LiteLLM.
2. [ ] Execute a conditional workflow (router node → branch A / branch B).
3. [ ] Execute a workflow with MCP tool calls.
4. [ ] Run status tracking (pending → running → completed/failed).
5. [ ] Run output collection and logging.

## Definition of Done
- Linear, conditional, and MCP-tool workflows execute with real LLM calls against actual running Gateway (`:8000`), Inference (`:8001`), and Postgres (`:5432`).
- Run status tracking and output collection/logging verified.
- If errors arise, inspect Docker container logs (`docker compose logs db redis`) and fix root causes directly in GuardRoute execution engine.
- Deleting test data is not necessary.
