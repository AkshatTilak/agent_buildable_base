# Subtask: Startup Sequence

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [ ] Load `.env` and validate required settings (fail-fast on missing critical keys).
- [ ] Initialize database connections (Postgres, Qdrant, Neo4j, Redis) with retry/backoff.
- [ ] Run Alembic migrations (if `APP_ENV != testing`).
- [ ] Load Model Registry from database; apply environment overrides.
- [ ] Iterate `ACTIVE_PROJECTS`: import `setup.py` hooks, call `init_app_state()`.
- [ ] Mount project API routers dynamically.
- [ ] Start OpenTelemetry instrumentation.
- [ ] Start serving requests.

## Dependencies
- All database clients. Alembic configuration. Model Registry.

## Definition of Done
- Gateway starts reliably with proper initialization ordering. Fails fast on missing config.
