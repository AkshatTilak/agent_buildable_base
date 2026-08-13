# Base Task 11: Interactive Visual Workflow Builder & Hub Integrations (v7)

## Objective
Complete and harden the Interactive Visual Workflow Builder and Multi-Hub Orchestration Engine. The V5/V6 workflow builder already ships a working visual canvas, graph parser, versioning, cross-hub resolver, and run/SSE plumbing. **This task is a verification-and-gap-closing pass**, not greenfield construction: confirm each layer is wired end-to-end, then close the concrete gaps that prevent real execution (the run service currently *simulates* node execution instead of running the compiled LangGraph), add missing step telemetry, and add the missing integration tests.

## Current State (verified against codebase)
- **Models** (`common/models/database.py`): `WorkflowDefinition`, `WorkflowVersion`, `WorkflowRun`, `EvalFlowTrace` already exist with hub scoping, versioning, and run persistence. No `WorkflowRunStep` model yet.
- **Schemas** (`common/schemas/workflows.py`): `NodeReference`, `WorkflowGraph`, `ValidationIssue`, `ValidationResult`, `WorkflowCreate/Update/Summary/Detail`, `WorkflowVersionSummary/Detail`, `WorkflowRunSummary/Detail` all exist.
- **Graph parser** (`projects/guardroute/src/core/graph_parser.py`): `GraphParser` with `SUPPORTED_NODE_TYPES`, `validate_graph()`, `build_langgraph()`, `validate_references()`, and module-level `validate_workflow_graph()` already implemented.
- **Orchestrator** (`projects/guardroute/src/orchestrator.py`): `GraphState` TypedDict and node functions exist.
- **Run service** (`projects/guardroute/src/workflows/run_service.py`): `start_run()`, `_execute_run_task()`, `stream_run()`, `cancel_run()`, `get_run()`, `list_runs()` exist with SSE. **Gap:** `_execute_run_task()` currently emits placeholder outputs (`"Executed {ntype} {curr_id}"`) instead of actually invoking the compiled LangGraph.
- **Versioning** (`projects/guardroute/src/workflows/version_service.py`): `compute_etag()`, `get_draft()`, `update_draft()`, `publish()`, `restore()`, `duplicate()`, `diff_versions()` exist.
- **Gateway API** (`gateway/api/workflows.py`): full CRUD + `POST /{wf_id}/validate` + `POST /{wf_id}/run` (SSE) + runs + traces + export already exist.
- **Cross-hub resolver** (`common/services/hub_resolver.py`): `resolve_linked()`, `resolve_linked_many()`, `assert_link()`, `list_linked_hub_ids()` exist.
- **Frontend** (`frontend/src/components/hubs/workflow/`): `WorkflowCanvas.tsx`, `WorkflowNodeCard.tsx`, `WorkflowEditor.tsx`, `WorkflowRunModal.tsx`, `WorkflowLibrary.tsx`, `WorkflowRuns.tsx`, `CreateWorkflowDialog.tsx` all exist.
- **Node executors** (`projects/guardroute/src/nodes/`): action, api_call, conditional_evaluator, eval, final_message, mcp_tool, multi_agent, router, transform, webhook exist. **Gap:** several (e.g. `mcp_tool_executor.py`) are simulated stubs.

> **Architecture note (B7-15):** Vector retrieval, MCP tools, and external database access are **no longer standalone workflow nodes** — they are **tools** bound to an agent node (`data.tools`). See [`15_agent_tools_architecture.md`](15_agent_tools_architecture.md). The `retrieval`, `mcp_tool`, `database_query`, and `db_store` node types are removed from `SUPPORTED_NODE_TYPES` and rejected during validation.

## Subtasks
1. `[x]` `sub_11_01_database_schema_and_models.md`: **Verify** existing workflow models/schemas against the graph contract; add the missing `WorkflowRunStep` telemetry model + Alembic migration; confirm indexes and hub-scoped query guards.
2. `[x]` `sub_11_02_backend_workflow_engine_and_langgraph.md`: **Close the execution gap** — make `_execute_run_task()` actually compile and run the LangGraph `StateGraph` per node type with handle-level data routing, conditional branching, parallel fan-in, and error-handle fallback; replace simulated node executors with real ones.
3. `[x]` `sub_11_03_cross_hub_resolution_and_validation.md`: **Verify** `resolve_linked()` integration and the existing `POST /{wf_id}/validate` endpoint; add missing integration tests (`test_workflow_graph_refs.py`, `test_workflow_hub.py`).
4. `[x]` `sub_11_04_frontend_visual_node_canvas_ux.md`: **Verify** the existing canvas/editor/run-modal UX is wired to the real run + validate endpoints; fix any drift between frontend node types and backend `SUPPORTED_NODE_TYPES`.

## Definition of Done
- `POST /hubs/{hub_id}/workflows/{id}/run` executes the **real** compiled graph (not placeholder outputs) and streams per-node SSE events.
- `WorkflowRunStep` rows are persisted per executed node with input/output state, latency, and status.
- `POST /hubs/{hub_id}/workflows/{id}/validate` returns node-level errors/warnings for cycles, dangling edges, non-terminal leaves, and missing/inactive cross-hub references.
- New integration tests pass and cover graph refs + hub validation.
- Frontend node palette matches backend `SUPPORTED_NODE_TYPES`; run modal consumes real run output.
