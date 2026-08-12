# Subtask 11_02: Backend Workflow Engine — Real LangGraph Execution

## Objective
Close the core execution gap: `run_service._execute_run_task()` currently **simulates** node execution with placeholder outputs (`"Executed {ntype} {curr_id}"`). Make it actually compile the visual graph into a LangGraph `StateGraph` via `GraphParser.build_langgraph()` and execute it per node type with handle-level data routing, conditional branching, parallel fan-in, and error-handle fallback. Replace simulated node executors with real implementations.

## Current State (verified)
- `GraphParser.build_langgraph()` (`projects/guardroute/src/core/graph_parser.py`) already compiles `graph_json.nodes`/`edges` into a `StateGraph(GraphState)` and wires edges. **Note:** the method is `build_langgraph()`, not `compile_graph()`.
- `GraphState` (`projects/guardroute/src/orchestrator.py`) already carries `prompt`, `subagent_results`, `final_response`, `webhook_results`, `api_call_results`, `eval_results`, `transform_outputs`, `mcp_tool_results`, `conditional_flags`.
- `run_service._execute_run_task()` (`projects/guardroute/src/workflows/run_service.py`) already does topological scheduling, SSE `node_start`/`node_end`/`run_end` events, `EvalFlowTrace` emission, and `WorkflowRun` persistence — but the node body is a placeholder.
- Node executors exist in `projects/guardroute/src/nodes/`; several (e.g. `mcp_tool_executor.py`) return simulated results.

## Tasks
1. `[x]` **Compile & execute the real graph in `_execute_run_task()`**:
   - Build `graph = GraphParser(graph_json).build_langgraph()` once at run start.
   - Seed `GraphState` from `input_json` (map `input` → `prompt`/state keys).
   - Replace the placeholder node body with `await graph.ainvoke(initial_state)`.
   - Keep the existing topological scheduling, SSE event emission, `EvalFlowTrace` emission, and `WorkflowRun` persistence; derive per-node `input_state`/`output_state` from the returned state.
2. `[x]` **Handle-level data routing** — ensure `build_langgraph()` maps `sourceHandle` (`out`, `true`, `false`, `error`, `route_*`) and `targetHandle` (`in`, `in_multi`) into LangGraph conditional edges:
   - `IfElseNode` → route to `true`/`false` based on `conditional_flags[node_id]`.
   - `RouterNode` → route to `route_<name>` based on `conditional_flags[node_id]`.
   - `GatherNode` → merge parallel branch outputs into an array (`in_multi`).
   - `error` handle → fallback branch on node failure.
3. `[x]` **Error-handle fallback** — if a node raises and an `error` handle is wired, capture the exception into `state["errors"][node_id]` and transition along the `error` branch instead of aborting the run. If no `error` handle is wired, mark the run `failed` with `error_message`.
4. `[x]` **Replace simulated node executors** in `projects/guardroute/src/nodes/`:
   - `mcp_tool_executor.py`: invoke the real MCP tool via `gateway/services/mcp_client.py` (or `common/services/mcp_client`) instead of returning a canned string.
   - `eval_executor.py`: keep heuristic fallback but wire to real EvalOps judge when available.
   - Verify `agent`/`retrieval`/`multi_agent`/`api_call`/`webhook`/`transform`/`coding` executors resolve their cross-hub references via `common/services/hub_resolver.resolve_linked()` before executing.
5. `[x]` **Persist `WorkflowRunStep` rows** — for each executed node, write a `WorkflowRunStep` (from subtask 11_01) with `input_state`, `output_state`, `status`, `latency_ms`, `sequence`.
6. `[x]` **Unit tests** in `tests/test_workflow_run_execution.py`:
   - A simple `start → transform → final_message` graph produces real transformed output.
   - An `if_else` graph routes to the correct branch.
   - A node wired to an `error` handle falls back instead of failing the run.
   - A node with no `error` handle fails the run with `error_message` set.
