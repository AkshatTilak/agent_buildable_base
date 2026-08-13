# Subtask 10_03: Real MCP Tools Live Tests

## Parent Base Task
`tasks/v8/base/10_live_api_tests.md`

## Objective
Create `tests/live_api/test_mcp_tools_real.py` — real MCP tool lifecycle & workflow integration.

## Tasks
1. [ ] Stand up a sample MCP server from `mcp_tools/` → register via API → health check → discover tools → invoke tool → verify response.
2. [ ] Test tool discovery via REST (`/tools`), JSON-RPC (`tools/list`), and direct probe endpoints.
3. [ ] Test tool invocation via REST (`/invoke`) and JSON-RPC (`tools/call`).
4. [ ] Test encrypted auth token round-trip (register with `bearer` auth → invoke with decrypted token).
5. [ ] Test tool execution within a GuardRoute workflow node (MCP tool executor).
6. [ ] Test DB-scoped MCP tools (`/hubs/{hub_id}/db-tools`) with real external DB credential.
7. [ ] Test tool enable/disable toggle → verify disabled tools are excluded from discovery.

## Definition of Done
- Real MCP tool discovery, invocation, auth round-trip, workflow integration, and enable/disable verified.
