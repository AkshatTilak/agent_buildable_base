# ST02_03: Configure Alembic Migrations

## Parent Link
`base/BT02_database_architecture.md`

## Actionable Steps
- [x] Step 1: Initialize Alembic with async template — `alembic.ini` created at repo root
- [x] Step 2: Configure `alembic.ini` and `env.py` to use async SQLAlchemy engine (`async_engine_from_config`)
- [x] Step 3: Import all models into `env.py` via `import app.models` (auto-registers `Base.metadata`)
- [x] Step 4: URL injected from `app.config.settings` (overrides alembic.ini value)
- [ ] Step 5: Run `alembic stamp head` against live DB to baseline current schema (requires Docker up)

## Dependencies
- `ST02_02_sqlalchemy_models.md` — ORM models must be defined ✅
- `alembic` package installed ✅
- PostgreSQL running for Step 5 (manual, requires Docker)

## Definition of Done
Alembic is configured for async PostgreSQL, `alembic stamp head` baselines current schema, future `alembic revision --autogenerate` diffs work correctly.

## Completion Notes
Implemented 2026-07-14:
- `echomind-core/alembic.ini`: `script_location = app/db/migrations`, asyncpg URL ✅
- `echomind-core/app/db/migrations/env.py`: async runner using `asyncio.run()` + `async_engine_from_config` ✅
- `compare_type=True` and `compare_server_default=True` for accurate autogenerate diffs ✅
- Step 5 (stamp) is a manual one-time action: `cd echomind-core && alembic stamp head`

## Stamp Command (run once after `docker compose up -d`):
```bash
cd echomind-core
alembic stamp head
```
