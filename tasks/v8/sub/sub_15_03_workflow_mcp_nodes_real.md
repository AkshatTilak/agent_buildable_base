# Subtask 15_03: MCP Nodes in Workflows Real Integration Tests

## Parent Base Task
`tasks/v8/base/15_mcp_tools_ecosystem.md`

## Objective
Create `tests/integration/guardroute/test_workflow_mcp_nodes_real.py` — MCP tools in workflows.

## Tasks
1. [x] Create workflow with MCP tool node → assign sample calculator tool.
2. [x] Execute workflow → verify MCP node calls the tool and passes result downstream.
3. [x] Test MCP node error handling (tool server down, timeout, invalid params).
4. [x] Test MCP node with auth (bearer token passed correctly).

## Definition of Done
- MCP-node-in-workflow test passes with result passing downstream against actual running Gateway (`:8000`).
- Error handling and auth verified.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
