# B5-05: MCP Integration Hub

> **Status:** `[x]`  
> **Owner:** `gateway`  
> **Secondary:** `projects/syntraflow`, `frontend`  
> **Complexity:** 🟡 Medium (5 subtasks)

---

## Objective

Provide a management layer for Model Context Protocol (MCP) servers and tools. Users can register external MCP servers, discover their tools, test-invoke them inline, and make all tools available to the Workflow Builder's node palette. SyntraFlow's existing MCP server auto-registers as an internal entry on startup.

---

## Acceptance Criteria

- [x] `mcp_servers` and `mcp_tool_cache` DB tables created
- [x] CRUD endpoints for MCP server registration (URL, transport, auth)
- [x] Tool discovery endpoint queries a server's `list_tools` and caches results
- [x] Tool invocation endpoint calls any registered tool by server_id + tool_name + params
- [x] Inline tool testing via API (provide sample input, get result)
- [x] SyntraFlow MCP server auto-registered as internal server on gateway startup
- [x] Health check polling for registered servers (status indicators)
- [x] Frontend MCPHubPage with server list, tool panels, test invoker, enable/disable toggles
- [x] Registered tools appear in Workflow Builder node palette as MCPToolNodes

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-05a | MCP Server Registry CRUD & Health Checks | [`S5-05a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-05a.md) |
| S5-05b | Tool Discovery & Caching | [`S5-05b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-05b.md) |
| S5-05c | Tool Invocation API & Testing | [`S5-05c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-05c.md) |
| S5-05d | Internal MCP Auto-Registration | [`S5-05d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-05d.md) |
| S5-05e | Frontend: MCPHubPage | [`S5-05e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-05e.md) |
