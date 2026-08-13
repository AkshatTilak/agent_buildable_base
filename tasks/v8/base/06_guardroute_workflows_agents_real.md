# Base Task 6: Real-World Integration Tests — GuardRoute (Workflows & Agents) (v8)

## Objective
Test workflow creation, execution, node-by-node processing, and agent CRUD against real services, including real LLM calls via LiteLLM.

## Why
Workflow execution and agent orchestration are the core GuardRoute responsibilities. Existing tests mock the LLM and node executors, so real model routing, node execution, and run-status tracking are never validated.

## Scope
- **`tests/integration/guardroute/test_agents_real.py`** — Agent CRUD:
  - Create agent with system prompt + model config → verify DB row.
  - Update agent → verify endpoint slug regeneration rules.
  - Delete agent → verify cleanup.
  - Agent-hub scoping (agent only accessible within its hub).
- **`tests/integration/guardroute/test_workflows_real.py`** — Workflow lifecycle:
  - Create workflow with canvas JSON → verify DB storage.
  - Workflow versioning (save new version → list versions → restore old version).
  - Workflow import/export (portability).
  - Workflow-hub scoping.
- **`tests/integration/guardroute/test_workflow_execution_real.py`** — Workflow runs:
  - Execute a simple linear workflow (input → LLM → output) using `gemini/gemini-3.5-flash` via LiteLLM.
  - Execute a conditional workflow (router node → branch A / branch B).
  - Execute a workflow with MCP tool calls.
  - Run status tracking (pending → running → completed/failed).
  - Run output collection and logging.
- **`tests/integration/guardroute/test_orchestrator_real.py`** — Node execution:
  - Test each node executor type (API call, transform, conditional, webhook, terminal, eval, db_query, db_store, mcp_tool, multi_agent) individually against real backends.
  - Multi-agent executor: test with `gemini/gemma-3-27b-it` as agent model.
  - Error handling per node type (timeout, invalid config, missing credentials).

## Associated Subtasks
1. `[ ]` `sub_06_01_agents_real.md`: `test_agents_real.py` — agent CRUD & hub scoping.
2. `[ ]` `sub_06_02_workflows_real.md`: `test_workflows_real.py` — workflow lifecycle & versioning.
3. `[ ]` `sub_06_03_workflow_execution_real.md`: `test_workflow_execution_real.py` — workflow runs with real LLM.
4. `[ ]` `sub_06_04_orchestrator_real.md`: `test_orchestrator_real.py` — node executors & error handling.

## Definition of Done
- Agent CRUD and hub scoping verified against real Postgres.
- Workflow lifecycle, versioning, import/export, and hub scoping verified.
- Linear, conditional, and MCP-tool workflows execute with real LLM calls and correct run-status tracking.
- Each node executor type tested individually with proper error handling.
