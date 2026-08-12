# Subtask 12_04: Frontend Database Connection Manager & Node Editor UX

## Objective
Deliver a frontend user interface for managing Hub-scoped external database connections, testing DB connectivity in real-time, and selecting database credentials in the visual workflow node property drawer.

> **Path corrections:** The `frontend/src/components/credentials/` directory does not exist yet — create it. API service functions go in `frontend/src/services/api.ts` under a new `dbCredentials` namespace (the existing `api.credentials` namespace is for provider API keys and must not be reused).

## UI Components & Integration

### 1. Database Connections Manager (`frontend/src/components/credentials/DatabaseConnectionsTab.tsx`)
- Tab in Hub Settings / Credentials view.
- **Connection List Table**: Displays profile name, database engine type badge (Postgres, MySQL, Mongo, Redis), host/port, read-only status, and last test health indicator.
- **"Add Connection" Modal (`CreateDatabaseConnectionDialog.tsx`)**:
  - Connection form: Name, DB Type, Host, Port, Database Name, Username, Password, SSL checkbox, Read-Only toggle.
  - "Test Connection" button calling `api.dbCredentials.test()`.

### 2. Workflow Builder Node Palette & Property Drawer Updates
- **Node Palette (`WorkflowEditor.tsx` & `WorkflowNodeCard.tsx`)**:
  - Add **Database Query Node** (`database_query`, icon: `Database`, color: `emerald`) under **Hub & Data Integrations**.
  - Add **DB Store Node** (`db_store`) if applicable.
- **Property Drawer (`WorkflowEditor.tsx`)**:
  - Displays **Select Database Connection** dropdown querying `api.dbCredentials.list(hubId)`.
  - SQL / Parametrized Query Code Editor with syntax highlighting for template variables (`:param_name`).
  - Query timeout slider & Max rows input.

## Tasks
1. `[x]` Create `DatabaseConnectionsTab.tsx` and `CreateDatabaseConnectionDialog.tsx` in `frontend/src/components/credentials/`.
2. `[x]` Add API service functions in `frontend/src/services/api.ts` under a new `dbCredentials` namespace for `/hubs/{hub_id}/db-credentials` (list/get/create/update/delete/test).
3. `[x]` Register `database_query` (and `db_store`) node types in `NODE_CONFIGS` in `WorkflowNodeCard.tsx` with input `in` and output `out`, `row_count`, `error` handles.
4. `[x]` Update `WorkflowEditor.tsx` property drawer to render the live Database Connection selector and SQL query parameter editor.
