# ST02_03: Configure Alembic Migrations

## Parent Link
`base/BT02_database_architecture.md`

## Actionable Steps
- [ ] Step 1: Initialize Alembic with async template (`alembic init -t async app/db/migrations`)
- [ ] Step 2: Configure alembic.ini and env.py to use async SQLAlchemy engine
- [ ] Step 3: Import all models into env.py target_metadata
- [ ] Step 4: Generate initial migration from ORM models
- [ ] Step 5: Test migration up/down against clean PostgreSQL instance

## Dependencies
- `ST02_02_sqlalchemy_models.md` — ORM models must be defined
- `alembic` package installed

## Definition of Done
Alembic is configured for async PostgreSQL, initial migration generated, and up/down verified.
