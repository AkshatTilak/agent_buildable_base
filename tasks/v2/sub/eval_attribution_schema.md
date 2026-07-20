# Subtask: Evaluation Attribution Database Schemas

**Parent Link:** `base/05_eval_test_generation.md`

## Actionable Steps
- [ ] Define SQLAlchemy database models in `projects/evalops/src/database/models.py`:
  - [ ] `EvalTestSuite`: Maps `agent_id` to a collection of test cases.
  - [ ] `EvalTestCase`: Stores individual test cases (columns: `id`, `suite_id`, `input_query`, `expected_output`, `expected_context`).
  - [ ] `EvalRunHistory`: Stores execution results (columns: `id`, `agent_id`, `faithfulness_score`, `relevance_score`, `duration_sec`, `run_status`, `created_at`).
- [ ] Generate database migration scripts via Alembic and run migrations.

## Dependencies
- `sub/agent_crud_schema.md`

## Definition of Done
- Tables successfully generated in Postgres.
- Test suites can be associated with agent records.
