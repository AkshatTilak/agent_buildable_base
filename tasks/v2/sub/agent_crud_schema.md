# Subtask: Agent CRUD Database Schemas & Migrations

**Parent Link:** `base/04_agent_management_crud.md`

## Actionable Steps
- [x] Define the `AgentDefinition` model in SQLAlchemy inside `common/models/database.py`:
  - Columns: `id` (int/uuid), `name` (str), `role` (str), `system_prompt` (text), `model_id` (str), `tools` (JSON array of strings), `temperature` (float), `max_tokens` (int), `created_at` (datetime), `updated_at` (datetime).
- [x] Define the matching Pydantic validation schemas in `common/schemas/agent_types.py` for API validation: `AgentCreate`, `AgentUpdate`, `AgentResponse`.
- [x] Generate a database migration script using Alembic (`alembic revision --autogenerate -m "add_agent_definitions"`).
- [x] Execute migration script (`alembic upgrade head`) and verify database schemas on Postgres.

## Dependencies
- None

## Definition of Done
- Database tables initialized on PostgreSQL.
- Pydantic models validated via unit tests testing standard boundary parameters.
