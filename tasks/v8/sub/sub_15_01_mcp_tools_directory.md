# Subtask 15_01: MCP Tools Directory & Sample Servers

## Parent Base Task
`tasks/v8/base/15_mcp_tools_ecosystem.md`

## Objective
Create the `mcp_tools/` directory with sample tool servers, `_template`, and comprehensive README.

## Tasks
1. [ ] Create `mcp_tools/` directory structure:
   ```
   mcp_tools/
   ├── README.md
   ├── sample_calculator/           # Simple arithmetic tool (REST + JSON-RPC)
   │   ├── server.py
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
2. [ ] Each sample server is a self-contained FastAPI app implementing:
   - REST (`/tools` list, `/invoke`) and JSON-RPC (`tools/list`, `tools/call`) protocols.
   - Proper `inputSchema` definitions.
   - Startable with `python server.py` on a configurable port.
3. [ ] Create `mcp_tools/README.md` covering:
   - What MCP is and how ContAIned discovers/registers tools.
   - Required endpoints (`/tools`, `/invoke`, or JSON-RPC `tools/list` + `tools/call`).
   - Authentication options (none, bearer, api_key) and token encryption at rest.
   - Step-by-step: create a tool server → register in ContAIned UI → use in agent/workflow.
   - Transport options (SSE, stdio, streamable_http).
   - Input schema specification (JSON Schema format).
   - Testing your tool locally before registering.

## Definition of Done
- `mcp_tools/` directory with `sample_calculator`, `sample_web_search`, `sample_code_executor`, and `_template` servers, each implementing REST + JSON-RPC.
- Comprehensive `mcp_tools/README.md` guide.
