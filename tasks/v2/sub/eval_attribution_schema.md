# Subtask: Evaluation Attribution Database Schemas

**Parent Link:** `base/05_eval_test_generation.md`

## Actionable Steps
- [x] Define SQLAlchemy database models in `common/models/database.py` and `projects/evalops/src/database/models.py`:
  - [x] `EvalTestSuite`: Maps `agent_id` to a collection of test cases.
  - [x] `EvalTestCase`: Stores individual test cases (columns: `id`, `suite_id`, `input_query`, `expected_output`, `expected_context`).
  - [x] `EvalRunHistory`: Stores execution results (columns: `id`, `agent_id`, `faithfulness_score`, `relevance_score`, `duration_sec`, `run_status`, `created_at`).
- [x] Generate database migration scripts via Alembic and run migrations.

## Dependencies
- `sub/agent_crud_schema.md`

## Definition of Done
- Tables successfully generated in Postgres.
- Test suites can be associated with agent records.
