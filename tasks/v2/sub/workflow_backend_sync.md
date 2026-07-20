# Subtask: Workflow Backend Sync

**Parent Link:** `base/02_visual_workflow_builder.md`

## Actionable Steps
- [ ] Create a `workflows` database table in Postgres to persist visual configurations (columns: `id`, `name`, `graph_json`, `is_active`, `created_at`).
- [ ] Build REST API endpoints in GuardRoute (`projects/guardroute/api.py`):
  - [ ] `GET /api/guardroute/workflows`: List all workflows.
  - [ ] `POST /api/guardroute/workflows`: Save/create a workflow.
  - [ ] `PUT /api/guardroute/workflows/{id}/activate`: Toggle active status.
- [ ] Refactor the orchestrator endpoint `/api/guardroute/chat` to load the active workflow from the database, parse it dynamically, and execute the generated LangGraph compiled graph.

## Dependencies
- `sub/workflow_graph_translation.md`
- `sub/workflow_ui_controls.md`

## Definition of Done
- Workflows are successfully persisted and reloaded from Postgres database.
- Chat routing logic dynamically adjusts behavior based on the active database-configured workflow.
