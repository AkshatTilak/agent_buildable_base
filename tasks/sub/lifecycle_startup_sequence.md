# Subtask: Startup Sequence

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Load `.env` and validate required settings (fail-fast on missing critical keys).
- [x] Initialize database connections (Postgres, Qdrant, Neo4j, Redis) with retry/backoff.
- [x] Run Alembic migrations (if `APP_ENV != testing`).
- [x] Load Model Registry from database; apply environment overrides.
- [x] Iterate `ACTIVE_PROJECTS`: import `setup.py` hooks, call `init_app_state()`.
- [x] Mount project API routers dynamically.
- [x] Start OpenTelemetry instrumentation.
- [x] Start serving requests.

## Dependencies
- All database clients. Alembic configuration. Model Registry.

## Definition of Done
- Gateway starts reliably with proper initialization ordering. Fails fast on missing config.
