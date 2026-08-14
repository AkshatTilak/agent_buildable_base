# Base Task 15: MCP Tools Ecosystem & User-Facing Tool Registry (v8)

## Objective
Create a user-facing `mcp_tools/` directory with sample MCP tool servers, documentation on how to build custom tools, and comprehensive tests validating the full tool lifecycle — from authoring to registration to invocation within workflows.

## Why
There is no canonical location for user-contributed MCP tool servers, no sample implementations, and no documentation on how to build/register custom tools. This task establishes the ecosystem and validates the full MCP lifecycle.

## Scope
- **`mcp_tools/` directory** (root-level, alongside `projects/`, `gateway/`, etc.) — The canonical location for user-contributed and sample MCP tool servers:
  ```
  mcp_tools/
  ├── README.md                    # How to build & register custom tools
  ├── sample_calculator/           # Simple arithmetic tool (REST + JSON-RPC)
  │   ├── server.py                # FastAPI MCP server
  │   ├── requirements.txt
  │   └── README.md
  ├── sample_web_search/           # DuckDuckGo search tool
  │   ├── server.py
  │   ├── requirements.txt
  │   └── README.md
  ├── sample_code_executor/        # Sandboxed Python code execution tool
  │   ├── server.py
  │   ├── requirements.txt
  │   └── README.md
  └── _template/                   # Copy-paste starter template
      ├── server.py                # Annotated skeleton with all MCP protocol endpoints
      ├── requirements.txt
      └── README.md
  ```
- **`mcp_tools/README.md`** — Comprehensive guide covering:
  - What MCP is and how ContAIned discovers/registers tools.
  - Required endpoints (`/tools`, `/invoke`, or JSON-RPC `tools/list` + `tools/call`).
  - Authentication options (none, bearer, api_key) and how tokens are encrypted at rest.
  - Step-by-step: create a tool server → register in ContAIned UI → use in agent/workflow.
  - Transport options (SSE, stdio, streamable_http).
  - Input schema specification (JSON Schema format for tool parameters).
  - Testing your tool locally before registering.
- **Sample tool servers** — Each sample is a self-contained FastAPI app that:
  - Implements both REST (`/tools` list, `/invoke`) and JSON-RPC (`tools/list`, `tools/call`) protocols.
  - Includes proper `inputSchema` definitions.
  - Can be started with `python server.py` on a configurable port.
  - Serves as a testable fixture for integration tests.
- **`tests/integration/gateway/test_mcp_lifecycle_real.py`** — Full MCP lifecycle test using sample tools:
  - Start `sample_calculator` server as subprocess fixture.
  - Register via `POST /api/mcp/servers` → verify DB row.
  - Health check → verify `healthy` status.
  - Discover tools → verify calculator tools appear in cache.
  - Invoke `add` tool with `{a: 2, b: 3}` → verify result `5`.
  - Toggle tool disabled → verify excluded from `GET /api/mcp/tools`.
  - Update server URL → re-sync → verify tools still work.
  - Delete server → verify cascading cleanup of tool cache.
  - Test internal server deletion block (`is_internal = True`).
- **`tests/integration/guardroute/test_workflow_mcp_nodes_real.py`** — MCP tools in workflows:
  - Create workflow with MCP tool node → assign sample calculator tool.
  - Execute workflow → verify MCP node calls the tool and passes result downstream.
  - Test MCP node error handling (tool server down, timeout, invalid params).
  - Test MCP node with auth (bearer token passed correctly).

## Associated Subtasks
1. `[ ]` `sub_15_01_mcp_tools_directory.md`: Create `mcp_tools/` directory with sample servers + `_template` + README.
2. `[ ]` `sub_15_02_mcp_lifecycle_real.md`: `test_mcp_lifecycle_real.py` — full MCP lifecycle.
3. `[ ]` `sub_15_03_workflow_mcp_nodes_real.md`: `test_workflow_mcp_nodes_real.py` — MCP tools in workflows.

## Definition of Done
- `mcp_tools/` directory with `sample_calculator`, `sample_web_search`, `sample_code_executor`, and `_template` servers, each implementing REST + JSON-RPC.
- Comprehensive `mcp_tools/README.md` guide.
- Full MCP lifecycle test passes against actual running Gateway (`:8000`) and Postgres (`:5432`) (register → health → discover → invoke → toggle → update → delete endpoint test → internal block).
- MCP-node-in-workflow test passes with error handling and auth.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in MCP manager or GuardRoute engine.
- Deleting test data is not necessary.
