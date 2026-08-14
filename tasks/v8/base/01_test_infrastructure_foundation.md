# Base Task 1: Test Infrastructure & Configuration Foundation (v8)

## Objective
Build the shared plumbing that all subsequent test tiers depend on: a dedicated test environment connecting to actual development services on standard ports, master pytest conftest with real-service fixtures, structured test logging, gateway/inference process handling on actual ports (`:8000`, `:8001`), and the reorganized `tests/` directory hierarchy.

## Why
Today there is no test infrastructure for hitting actual services, managing `.env.test`, or running ordered multi-step flows. Mocks hide real-world bugs (schema drift, query failures, streaming issues). Tests must hit actual services, diagnose failures via Docker container logs, and drive fixes across submodules.

## Scope
- **`.env.test`** — Dedicated test environment file with real service credentials (Postgres `:5432`, Qdrant `:6333`, Redis `:6379`, Neo4j `:7687`, Kafka `:9092`, Gateway `:8000`, Inference `:8001`, LiteLLM API keys). Gitignored but with a `.env.test.example` template committed.
- **`conftest.py` (root)** — Master pytest conftest that:
  - Loads `.env.test` and provides a `test_settings` fixture.
  - Exposes `pytest.mark.unit`, `pytest.mark.integration`, `pytest.mark.e2e`, `pytest.mark.live_api`, `pytest.mark.streaming`, `pytest.mark.performance` markers.
  - Provides a `real_db_session` async fixture connecting to the actual Postgres database.
  - Provides `qdrant_client`, `redis_client`, `neo4j_driver` fixtures connected to actual running services.
  - Provides a `gateway_client` fixture (httpx `AsyncClient` against the running gateway on `:8000` or TestClient-mounted gateway).
  - Provides `seed_user`, `seed_hub`, `seed_workflow` factory fixture chain for ordered data creation using unique identifiers.
- **Actual Services & Ports** — Tests run against actual running development Docker services and standard ports. No separate test compose stacks with divergent ports. Deleting test data is not necessary.
- **Structured test logging** — pytest JSON reporter + custom log formatter capturing test name/marker/duration, all HTTP requests/responses, DB queries (count, slow >100ms), assertion failures, and streaming event counts.
- **Gateway/Inference process management** — Process fixtures/health checks for gateway (`--port 8000`) and inference (`--port 8001`).
- **`pytest.ini` / `pyproject.toml` updates** — Register custom markers, configure `testpaths`, set `--strict-markers`.
- **Test directory restructure** — Reorganize flat `tests/` into `unit/`, `integration/` (gateway/syntraflow/guardroute/evalops), `streaming/`, `e2e/` (flows/contracts), `live_api/`, `performance/`.

## Associated Subtasks
1. `[x]` `sub_01_01_test_env_and_compose.md`: `.env.test` / `.env.test.example` targeting actual dev services and ports.
2. `[x]` `sub_01_02_root_conftest_fixtures.md`: Root `conftest.py` with markers, real-service fixtures, seed factories, and gateway client.
3. `[x]` `sub_01_03_process_fixtures_and_logging.md`: Gateway/Inference process handling on standard ports (`:8000`, `:8001`), structured test logging, and pytest config updates.
4. `[x]` `sub_01_04_test_directory_restructure.md`: Reorganize `tests/` into the layered hierarchy.

## Definition of Done
- `.env.test` connects to actual running dev services on standard ports (`:8000`, `:8001`, `:5432`, `:6333`, `:6379`, `:7687`, `:9092`).
- `pytest tests/unit/ -m unit` runs with no external dependencies.
- Real-service fixtures connect to Postgres/Qdrant/Redis/Neo4j successfully.
- Gateway/Inference processes verified healthy on standard ports (`:8000`, `:8001`).
- Structured JSON logs are written per test to `tests/logs/{run_timestamp}/`.
- Test data persistence accommodated without mandatory teardown wipes.
