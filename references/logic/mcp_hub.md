# MCP Registry Architecture

> **Source:** V5 Base Task B5-05 Implementation  
> **Status:** Completed `[x]`  
> **Last Updated:** 2026-07-24  
> **Files:**  
> - Backend Models: [database.py](file:///c:/Akshat/ContAIned/common/models/database.py)  
> - Router: [mcp_manager.py](file:///c:/Akshat/ContAIned/gateway/api/mcp_manager.py)  
> - Service Client: [mcp_client.py](file:///c:/Akshat/ContAIned/gateway/services/mcp_client.py)  
> - UI Component: [MCPHubPage.tsx](file:///c:/Akshat/ContAIned/frontend/src/components/MCPHubPage.tsx)  

---

## 0. ⚠️ V6 Rename Notice — "MCP Integration Hub" → **"MCP Registry"**

In V6 the word **Hub** is reserved exclusively for the tenancy construct defined in
[`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md). The V5 feature
called the *"MCP Integration Hub"* is therefore **renamed to the "MCP Registry"** to remove the
collision. Every user-facing label, heading, breadcrumb and doc reference uses the new name.

- **The route does not change:** it remains `/mcp` (and `/api/mcp/*` for the backend).
- The file name `mcp_hub.md`, the component file `MCPHubPage.tsx` and the router `mcp_manager.py` are
  retained for continuity; only the **displayed name** and prose change.
- Any remaining "MCP Hub" string in the UI or docs is a bug.

---

## 1. Overview

Central management layer for Model Context Protocol (MCP) servers. Supports registering external MCP servers, discovering tools, invoking tools, and surfacing them in the Workflow Builder.

The MCP Registry is a **platform-level** service, not a hub
([`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §7):

| Operation | Required access |
|---|---|
| Read (list servers, list/inspect tools, health) | any authenticated `active` user |
| Write (register, edit, delete, sync, enable/disable a tool) | platform `admin` only |

Tool **invocation** is performed by hub-scoped callers (agents, workflow nodes), which are authorised
by their own hub role; the registry itself grants no hub access.

---

## 2. Server Registry

- `mcp_servers` table tracks registered servers (internal + external)
- Internal: SyntraFlow's `mcp_server.py` auto-registered on startup
- External: user-registered via dashboard (platform `admin`)
- Supported transports: SSE, stdio, Streamable HTTP
- Auth: none, bearer token, API key (encrypted at rest via Fernet)

### Visibility scope (V6)

`mcp_servers.hub_id` is **nullable**:

| `hub_id` | Meaning | Visible to |
|---|---|---|
| `null` | **Shared platform registry entry** | every authenticated user; writable by platform `admin` |
| set | **Hub-private server**, registered for one hub | members of that hub only; writable by `owner`/`maintainer` of that hub |

Hub-private servers never appear in another hub's node palette or tool list, and are queried with a
`hub_id` predicate like any other hub-scoped row. Listing the registry returns the union of the shared
entries and the hub-private entries the caller can reach.

---

## 3. Tool Discovery

```
Register Server → call MCP list_tools() → Cache tools in mcp_tool_cache →
  → Display in the MCP Registry page (/mcp) → Available in Workflow Builder node palette
```

- Tool cache refreshed: on registration, on manual sync, on health check pass
- Schema stored: tool name, description, input_schema (JSON Schema)
- Tools can be individually enabled/disabled (platform `admin` for shared entries)
- The palette shows shared tools plus the current hub's private tools only

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
  → Resolve server config from DB (asserting the caller may see this server)
  → Connect via MCP client (transport-specific)
  → Invoke tool with params
  → Return result + timing
```

- Timeout: 30 seconds (configurable)
- Auth applied based on server config
- A hub-private server (`hub_id` set) is invocable only by callers inside that hub; anyone else
  receives `404`

---

## 6. Internal Auto-Registration

On gateway startup (in `setup.py` lifespan hook):
1. Check if SyntraFlow MCP server exists in `mcp_servers`
2. If not, insert with `is_internal=True` and `hub_id=None` (shared platform entry)
3. Trigger tool discovery
4. Future submodules can call `register_mcp_server()` utility

---

## 7. Database Tables

- `mcp_servers` — id, name, url, transport, auth_type, auth_token_encrypted, `hub_id` (**nullable**;
  `null` = shared platform registry entry, set = hub-private server), is_internal, is_active,
  health_status, last_health_check, timestamps
- `mcp_tool_cache` — id, server_id (FK), tool_name, description, input_schema_json, is_enabled, last_synced

---

## 8. Workflow Builder Integration

- `MCPToolNode` in workflow builder references cached tools
- Node palette auto-populates "Tools" category from `GET /api/mcp/tools`
- At execution time, graph parser compiles MCPToolNode → async node calling `mcp_tool_executor`
