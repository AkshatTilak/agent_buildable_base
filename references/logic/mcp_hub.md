# MCP Integration Hub Architecture

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

## 1. Overview

Central management layer for Model Context Protocol (MCP) servers. Supports registering external MCP servers, discovering tools, invoking tools, and surfacing them in the Workflow Builder.

---

## 2. Server Registry

- `mcp_servers` table tracks registered servers (internal + external)
- Internal: SyntraFlow's `mcp_server.py` auto-registered on startup
- External: user-registered via dashboard
- Supported transports: SSE, stdio, Streamable HTTP
- Auth: none, bearer token, API key (encrypted at rest via Fernet)

---

## 3. Tool Discovery

```
Register Server → call MCP list_tools() → Cache tools in mcp_tool_cache →
  → Display in MCPHubPage → Available in Workflow Builder node palette
```

- Tool cache refreshed: on registration, on manual sync, on health check pass
- Schema stored: tool name, description, input_schema (JSON Schema)
- Tools can be individually enabled/disabled

---

## 4. Health Monitoring

- Periodic health checks every 5 minutes (background task)
- Status: healthy / unhealthy / unknown
- SSE transport: HTTP GET to server URL
- Health status displayed with color indicators in UI

---

## 5. Tool Invocation

```
POST /api/mcp/tools/invoke → { server_id, tool_name, parameters }
  → Resolve server config from DB
  → Connect via MCP client (transport-specific)
  → Invoke tool with params
  → Return result + timing
```

- Timeout: 30 seconds (configurable)
- Auth applied based on server config

---

## 6. Internal Auto-Registration

On gateway startup (in `setup.py` lifespan hook):
1. Check if SyntraFlow MCP server exists in `mcp_servers`
2. If not, insert with `is_internal=True`
3. Trigger tool discovery
4. Future submodules can call `register_mcp_server()` utility

---

## 7. Database Tables

- `mcp_servers` — id, name, url, transport, auth_type, auth_token_encrypted, is_internal, is_active, health_status, last_health_check, timestamps
- `mcp_tool_cache` — id, server_id (FK), tool_name, description, input_schema_json, is_enabled, last_synced

---

## 8. Workflow Builder Integration

- `MCPToolNode` in workflow builder references cached tools
- Node palette auto-populates "Tools" category from `GET /api/mcp/tools`
- At execution time, graph parser compiles MCPToolNode → async node calling `mcp_tool_executor`
