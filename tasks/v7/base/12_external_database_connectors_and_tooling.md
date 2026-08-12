# Base Task 12: External Database Connectors & Database Tooling Nodes (v7)

## Objective
Establish a secure, hub-isolated external database connector infrastructure enabling workflows and LLM Agents to query external relational (Postgres, MySQL), NoSQL (MongoDB, Redis), and Data Warehouse (Snowflake, BigQuery) databases. Provides encrypted credential management, dynamic MCP database tools for LLM agents, and dedicated visual workflow database query nodes (`DatabaseQueryNode`, `DBStoreNode`).

> **Note on scope:** This is largely **new** work (unlike Task 11). The `ExternalCredential` model, `common/clients/db_connectors/` package, and `common/services/mcp_db_bridge.py` do not yet exist. The task paths below are corrected to match the actual codebase layout.

## Current State (verified against codebase)
- **Encryption:** `common/security/crypto.py` already provides Fernet-based `encrypt_secret()` / `decrypt_secret()` / `mask_connection_uri()`. **Reuse this** — do not create a separate AES-256-GCM `vault.py`.
- **Existing DB clients:** `common/clients/` has `postgres.py` (SQLAlchemy async), `redis.py`, `neo4j.py`, `qdrant.py`, `inference.py`, `litellm.py`. No `db_connectors/` package yet.
- **MCP lives in the Gateway**, not a `projects/mcp_manager/` package: `gateway/api/mcp_manager.py` (router prefix `/mcp`) and `gateway/services/mcp_client.py` (health, tool discovery, tool invocation, Fernet token encryption). `gateway/api/mcp_manager.py` exposes `/mcp/servers`, `/mcp/tools`, `/mcp/tools/invoke`.
- **Existing credentials API** (`gateway/api/credentials.py`, prefix `/settings/credentials`) manages **provider API keys** (OpenAI/Google/etc.) — a different concept. The new external DB credentials need a **separate hub-scoped router** (e.g. `/hubs/{hub_id}/db-credentials`).
- **Workflow node executors** live in `projects/guardroute/src/nodes/`; `mcp_tool_executor.py` currently returns simulated results.
- **Frontend:** no `frontend/src/components/credentials/` directory yet.

## Architecture & Infrastructure Design

```mermaid
graph TD
    UI[Frontend Connection Manager / Workflow Builder] --> API[Gateway DB Credentials API /hubs/{hub_id}/db-credentials]
    API --> SEC[common/security/crypto.py Fernet Vault]
    
    subgraph External Database Infrastructure
        CONN[Connector Pool Manager asyncpg / aiomysql / motor / redis] --> Psql[(Postgres / MySQL)]
        CONN --> Mongo[(MongoDB / Redis)]
    end
    
    SEC --> CONN
    
    subgraph Execution Channels
        CONN --> MCP[common/services/mcp_db_bridge.py]
        CONN --> NODE[GuardRoute DatabaseQueryNode Executor]
    end
    
    MCP --> AGENT[Agent Hub LLM Agents via gateway/services/mcp_client.py]
    NODE --> FLOW[LangGraph Workflow Execution]
```

## Subtasks
1. `[x]` `sub_12_01_encrypted_credentials_and_db_connectors.md`: `ExternalCredential` ORM model + Alembic migration, Fernet credential encryption (reuse `common/security/crypto.py`), async connector pool manager (`common/clients/db_connectors/`), read-only + row-limit + timeout guardrails, and hub-scoped Gateway credentials API.
2. `[x]` `sub_12_02_database_mcp_tool_bridge.md`: Dynamic MCP tool bridge (`common/services/mcp_db_bridge.py`) exposing DB connections as LLM agent tools, registered through the existing Gateway MCP registry (`gateway/api/mcp_manager.py`).
3. `[x]` `sub_12_03_workflow_database_query_nodes.md`: Dedicated visual workflow nodes (`DatabaseQueryNode`, `DBStoreNode`), LangGraph node executors, handles (`in_params`, `out_rows`, `out_error`), and parametrized query evaluation.
4. `[x]` `sub_12_04_frontend_db_connector_management_ui.md`: React UI for managing external database connection profiles, query test playground, and workflow builder property drawer database node selectors.

## Definition of Done
- `ExternalCredential` rows are hub-scoped, encrypted at rest (Fernet), and never return secrets in API responses.
- `common/clients/db_connectors/` supports Postgres, MySQL, MongoDB, Redis (Snowflake/BigQuery optional) with read-only enforcement, row limits, and timeouts.
- DB connections are exposed as MCP tools through the existing Gateway MCP registry and callable by Agent Hub LLM agents.
- `DatabaseQueryNode` / `DBStoreNode` execute in GuardRoute workflows with parametrized queries and error-handle fallback.
- Frontend connection manager + workflow property drawer selectors are wired to the new API.

