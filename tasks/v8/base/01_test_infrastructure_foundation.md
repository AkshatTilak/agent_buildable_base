# Base Task 1: Test Infrastructure & Configuration Foundation (v8)

## Objective
Build the shared plumbing that all subsequent test tiers depend on: a dedicated test environment, master pytest conftest with real-service fixtures, a full Docker Compose test stack, structured test logging, gateway/inference subprocess fixtures, and the reorganized `tests/` directory hierarchy.

## Why
Today there is no test infrastructure for standing up real Docker services, managing `.env.test`, seeding/cleaning databases, or running ordered multi-step flows. Every test mocks external services with `sqlite+aiosqlite:///:memory:` and `MagicMock`, so real-world bugs (schema drift, query failures, streaming issues) go undetected.

## Scope
- **`.env.test`** — Dedicated test environment file with real service credentials (Postgres, Qdrant, Redis, Neo4j, LiteLLM API keys). Gitignored but with a `.env.test.example` template committed.
- **`conftest.py` (root)** — Master pytest conftest that:
  - Loads `.env.test` and provides a `test_settings` fixture.
  - Exposes `pytest.mark.unit`, `pytest.mark.integration`, `pytest.mark.e2e`, `pytest.mark.live_api`, `pytest.mark.streaming`, `pytest.mark.performance` markers.
  - Provides a `real_db_session` async fixture connecting to a real Postgres test database, wrapping each test in a transaction and rolling back.
  - Provides `qdrant_client`, `redis_client`, `neo4j_driver` fixtures connected to real services.
  - Provides a `gateway_client` fixture (httpx `AsyncClient` against a running or TestClient-mounted gateway).
  - Provides `seed_user`, `seed_hub`, `seed_workflow` factory fixture chain for ordered data creation.
- **`docker-compose.test.yml`** — Full compose file starting all infra services (Postgres, Qdrant, Redis, Neo4j, Kafka + Zookeeper) with ephemeral volumes. Runs once per test session (not per-test). Torn down and cleaned after all tests complete. Gateway and Inference are **not** Dockerized — started via `poetry run uvicorn` subprocess management within conftest.
- **Structured test logging** — pytest JSON reporter + custom log formatter capturing test name/marker/duration, all HTTP requests/responses, DB queries (count, slow >100ms), assertion failures, and streaming event counts.
- **Gateway/Inference process fixtures** — Session-scoped async fixtures starting gateway (`--port 8100`) and inference (`--port 8110`), waiting for health checks, killing on teardown.
- **`pytest.ini` / `pyproject.toml` updates** — Register custom markers, configure `testpaths`, set `--strict-markers`.
- **Test directory restructure** — Reorganize flat `tests/` into `unit/`, `integration/` (gateway/syntraflow/guardroute/evalops), `streaming/`, `e2e/` (flows/contracts), `live_api/`, `performance/`.

## Associated Subtasks
1. `[x]` `sub_01_01_test_env_and_compose.md`: `.env.test` / `.env.test.example` and `docker-compose.test.yml` with ephemeral volumes.
2. `[x]` `sub_01_02_root_conftest_fixtures.md`: Root `conftest.py` with markers, real-service fixtures, seed factories, and gateway client.
3. `[x]` `sub_01_03_process_fixtures_and_logging.md`: Gateway/Inference subprocess fixtures, structured test logging, and pytest config updates.
4. `[x]` `sub_01_04_test_directory_restructure.md`: Reorganize `tests/` into the layered hierarchy.

## Definition of Done
- `docker compose -f infrastructure/docker-compose.test.yml up -d` starts all services with ephemeral volumes; `down -v` cleans up.
- `pytest tests/unit/ -m unit` runs with no external dependencies.
- Real-service fixtures connect to Postgres/Qdrant/Redis/Neo4j and roll back per test.
- Gateway/Inference subprocess fixtures start, pass health checks, and are killed on teardown.
- Structured JSON logs are written per test to `tests/logs/{run_timestamp}/`.
