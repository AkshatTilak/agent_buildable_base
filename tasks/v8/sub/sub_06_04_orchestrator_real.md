# Subtask 06_04: Orchestrator Real Integration Tests

## Parent Base Task
`tasks/v8/base/06_guardroute_workflows_agents_real.md`

## Objective
Create `tests/integration/guardroute/test_orchestrator_real.py` — node executors & error handling.

## Tasks
1. [x] Test each node executor type (API call, transform, conditional, webhook, terminal, eval, db_query, db_store, mcp_tool, multi_agent) individually against real backends.
2. [x] Multi-agent executor: test with `gemini/gemma-3-27b-it` as agent model.
3. [x] Error handling per node type (timeout, invalid config, missing credentials).

## Definition of Done
- Each node executor type tested individually against actual running backends on standard ports.
- Multi-agent executor tested with `gemini/gemma-3-27b-it`.
- Error handling verified per node type.
- If errors arise, inspect Docker container logs (`docker compose logs db redis qdrant`) and fix root causes directly in node executor code.
- Deleting test data is not necessary.
