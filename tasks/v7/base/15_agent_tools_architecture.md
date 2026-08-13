# Base Task 15: Agent Tools Architecture — Remove Standalone Tool Nodes (v7)

## Objective
Refactor the Interactive Visual Workflow Builder so that **vector retrieval, MCP tools, and external database access are no longer standalone workflow nodes**. These capabilities are realistically only usable as **tools** that an agent invokes during its turn. Agents therefore carry **multiple selectable, accessible tool bindings**, and the workflow canvas exposes a tool-binding editor on the agent node instead of separate retrieval / MCP / DB nodes.

This task defines **what comprises a tool** and **what is allowed as a tool for agents**, then migrates the backend graph engine, validation, executors, and the frontend palette / property drawer to the new model.

## What is a Tool?
A **tool** is a capability an agent can invoke during its turn (function-calling), as opposed to a flow step in the workflow DAG. Each tool is a structured `ToolBinding`:

| `type` | Meaning | Required refs |
|---|---|---|
| `retrieval` | Vector search over an Ingestion Hub collection | `hub_id`, `collection_id` |
| `mcp` | A registered MCP server tool | `server_id`, `tool_name` |
| `db` | A read-only query over an `ExternalCredential` | `credential_id` |
| `web_search` | Web search capability | — |
| `api_call` | An external HTTP API call | `url` |

Common fields: `label`, `enabled`. Tools are bound to an **agent node** in the workflow graph (`data.tools`), not placed as standalone nodes.

## Current State (verified against codebase)
- **Backend** (`projects/guardroute/src/core/graph_parser.py`): `SUPPORTED_NODE_TYPES` previously included `RetrievalNode/retrieval`, `MCPToolNode/mcp_tool`, `DatabaseQueryNode/database_query`, `DBStoreNode/db_store` as standalone nodes.
- **Executors** (`projects/guardroute/src/nodes/`): `db_query_executor.py`, `db_store_executor.py`, `mcp_tool_executor.py`, `api_call_executor.py` exist and are reused as tool backends.
- **Frontend** (`frontend/src/components/hubs/workflow/`): palette + property drawer had retrieval / MCP / DB node entries; `CreateWorkflowDialog` RAG template used a standalone `retrieval` node.
- **Agent model** (`common/models/database.py::AgentDefinition`): `tools` is a flat `list[str]` (names only) — not structured bindings.

## Changes Made

### Backend
1. **`common/schemas/workflows.py`** — added `ToolBinding` schema (structured tool binding with per-type required-ref validation); extended `NodeReference.type` Literal with `credential`.
2. **`projects/guardroute/src/core/graph_parser.py`**:
   - Removed `retrieval`, `mcp_tool`, `database_query`, `db_store` from `SUPPORTED_NODE_TYPES`; added `REMOVED_NODE_TYPES`.
   - `validate_graph()` rejects removed node types with an actionable message.
   - `validate_workflow_graph()` emits structured `REMOVED_NODE_TYPE` errors and validates agent-node tool bindings (`UNKNOWN_TOOL_TYPE`, `TOOL_MISSING_REF`, `MALFORMED_TOOL`).
   - `validate_references()` validates tool bindings as cross-hub references.
   - `build_langgraph()` adds an explicit agent-node handler that invokes bound tools; removed the standalone retrieval/MCP/DB handlers.
3. **`projects/guardroute/src/nodes/tool_executor.py`** (new) — `execute_agent_tools()` dispatches tool bindings to the underlying executors (retrieval / mcp / db / web_search / api_call), capturing per-tool failures.
4. **`projects/guardroute/src/orchestrator.py`** — added `tool_results` to `GraphState`.

### Frontend
5. **`WorkflowNodeCard.tsx`** — removed retrieval / MCP / DB node configs + ports; agent node shows bound tools.
6. **`WorkflowEditor.tsx`** — removed retrieval / MCP / DB palette buttons and property-drawer sections; added a **Tool Bindings editor** (`ToolBindingAdder`) to the agent node property drawer.
7. **`CreateWorkflowDialog.tsx`** — RAG template now binds a `retrieval` tool to the agent node instead of a standalone retrieval node.
8. **`components/nodes/AgentNode.tsx`** — renders structured tool bindings.

### Tests
9. **`tests/test_workflow_tools.py`** (new) — covers `ToolBinding` schema, removed-node rejection, tool-binding validation, and tool-executor dispatch.
10. **`tests/test_v2_graph_parser.py`** — updated valid-topology test to use valid node types; added removed-node rejection test.

## Definition of Done
- `retrieval`, `mcp_tool`, `database_query`, `db_store` are **not** in `SUPPORTED_NODE_TYPES` and are rejected with a clear validation error.
- Agent nodes accept multiple structured tool bindings (`data.tools`) and execute them via `execute_agent_tools()`.
- The frontend palette no longer offers retrieval / MCP / DB nodes; the agent property drawer has a tool-binding editor.
- The RAG starter template binds a retrieval tool to the agent node.
- New + updated tests pass.
