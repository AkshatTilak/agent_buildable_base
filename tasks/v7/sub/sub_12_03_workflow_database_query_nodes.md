# Subtask 12_03: Dedicated Workflow Database Query & Store Nodes

## Objective
Implement dedicated visual workflow nodes (`DatabaseQueryNode`, `DBStoreNode`) in GuardRoute allowing workflows to execute parametrized database queries or persist state data directly into external databases as a first-class workflow step.

> **Path corrections:** Node executors live in `projects/guardroute/src/nodes/`; node-type registration happens in `projects/guardroute/src/core/graph_parser.py` (`SUPPORTED_NODE_TYPES` + `NODE_REFERENCE_REQUIREMENTS`). Credential resolution uses the new `ExternalCredential` model from subtask 12_01.

## Node Specs & Port Handles

### 1. `DatabaseQueryNode` (`database_query`)
- **Category**: Hub / Integration / Database
- **Inputs**:
  - `in`: Incoming payload / parameters object (`{ "user_id": 123, "status": "active" }`)
- **Outputs**:
  - `out`: Query result rows array (`[ { "id": 123, "name": "Akshat", "role": "engineer" } ]`)
  - `row_count`: Integer count of returned rows
  - `error`: Exception payload if DB connection or query execution fails
- **Node Config Data (`data`)**:
  - `credential_id`: UUID of linked external database credential
  - `query_template`: Parametrized SQL or JSON query (e.g. `SELECT * FROM users WHERE user_id = :user_id`)
  - `timeout_s`: Integer (default 30s)
  - `max_rows`: Integer (default 500)

### 2. `DBStoreNode` (`db_store`)
- **Category**: Action / Database
- **Inputs**:
  - `in`: Data record object to insert or update
- **Outputs**:
  - `out`: Primary key or affected row count
  - `error`: Error handle
- **Node Config Data (`data`)**:
  - `credential_id`: UUID of linked external database credential
  - `target_table`: Table or collection name
  - `operation`: Enum (`insert`, `upsert`, `append`)

## Tasks
1. `[x]` Register `database_query` and `db_store` in `SUPPORTED_NODE_TYPES` and `NODE_REFERENCE_REQUIREMENTS` in `projects/guardroute/src/core/graph_parser.py` (map to a new `credential` reference type).
2. `[x]` Implement `execute_database_query_node` in `projects/guardroute/src/nodes/db_query_executor.py`:
   - Resolve `credential_id` via the `ExternalCredential` model (hub-scoped).
   - Parse the parametrized SQL template using incoming state variables (`state["input"]` or upstream step outputs).
   - Execute the query via `common.clients.db_connectors`.
   - Route the output array to the `out` handle or transition to the `error` handle on failure.
3. `[x]` Implement `execute_db_store_node` in `projects/guardroute/src/nodes/db_store_executor.py` for `insert`/`upsert`/`append` operations.
4. `[x]` Update `GraphParser` topology validation to verify the linked `credential_id` exists in the target Hub.
5. `[x]` Write unit tests in `tests/test_workflow_db_nodes.py` verifying parametrized SQL query resolution and node execution.
