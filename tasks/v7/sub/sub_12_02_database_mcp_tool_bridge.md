# Subtask 12_02: External Database MCP Tool Bridge for LLM Agents

## Objective
Build a dynamic MCP (Model Context Protocol) tool bridge that automatically exposes registered external database connection profiles as executable MCP tools. LLM Agents (`AgentNode`) in Agent Hub can inspect database schemas and run dynamic queries via standardized MCP tool calls.

> **Path corrections:** MCP infrastructure lives in the **Gateway**, not a `projects/mcp_manager/` package. The registry is `gateway/api/mcp_manager.py` (router prefix `/mcp`) and the client is `gateway/services/mcp_client.py`. The new bridge belongs in `common/services/mcp_db_bridge.py`.

## Architecture & Integration Design

### 1. Dynamic Database MCP Tool Registration (`common/services/mcp_db_bridge.py`)
Automatically generate 3 standard MCP tool definitions per registered `ExternalCredential`:

1. **`db_schema_inspector`**:
   - **Input Schema**: `{ "credential_id": "str", "table_name": "Optional[str]" }`
   - **Output**: Returns JSON list of tables, column names, data types, primary keys, and foreign key relations.
2. **`db_query_executor`**:
   - **Input Schema**: `{ "credential_id": "str", "sql_query": "str", "params": "Optional[dict]" }`
   - **Behavior**: Executes read-only SQL query via `PostgresConnector` / `MySQLConnector`, returning JSON rows.
3. **`mongo_collection_query`**:
   - **Input Schema**: `{ "credential_id": "str", "collection": "str", "filter": "dict", "limit": "int" }`
   - **Behavior**: Queries MongoDB collection filter via `MongoConnector`.

### 2. LLM Agent Integration
- In Agent Hub (`projects/guardroute/src/`), when an agent is configured with database tool access, the GuardRoute orchestrator injects database tool schemas into the model's system prompt or OpenAI tool function calls.
- Agent handles multi-turn tool calling:
  - Step 1: Agent calls `db_schema_inspector` to discover database structure.
  - Step 2: Agent synthesizes an appropriate SQL query.
  - Step 3: Agent calls `db_query_executor` to fetch data.
  - Step 4: Agent answers the user query based on database results.

## Tasks
1. `[x]` Implement the Database MCP tool generator in `common/services/mcp_db_bridge.py` mapping `ExternalCredential` profiles to MCP JSON-RPC tool schemas.
2. `[x]` Register database MCP tools through the existing Gateway MCP registry (`gateway/api/mcp_manager.py`) and expose them via `/mcp/tools` (or a hub-scoped `/hubs/{hub_id}/mcp/tools` variant). Reuse `gateway/services/mcp_client.py` patterns for tool invocation.
3. `[x]` Update the `MCPToolNode` handler in GuardRoute (`projects/guardroute/src/nodes/mcp_tool_executor.py`) to handle database tool execution results, formatting tabular SQL outputs into Markdown tables for agent context (replacing the current simulated stub).
4. `[x]` Write integration tests in `tests/test_db_mcp_tools.py` verifying schema inspection and query execution by LLM agent tool calls.
