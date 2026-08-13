# Subtask 01_02: Root conftest.py — Markers & Real-Service Fixtures

## Parent Base Task
`tasks/v8/base/01_test_infrastructure_foundation.md`

## Objective
Create the master pytest conftest that loads `.env.test`, registers markers, and provides real-service fixtures.

## Tasks
1. [ ] Load `.env.test` and provide a `test_settings` fixture.
2. [ ] Register markers: `unit`, `integration`, `e2e`, `live_api`, `streaming`, `performance`.
3. [ ] Provide `real_db_session` async fixture:
   - Connects to a real Postgres test database.
   - Wraps each test in a transaction and rolls back.
4. [ ] Provide `qdrant_client`, `redis_client`, `neo4j_driver` fixtures connected to real services.
5. [ ] Provide `gateway_client` fixture (httpx `AsyncClient` against a running or TestClient-mounted gateway).
6. [ ] Provide `seed_user`, `seed_hub`, `seed_workflow` factory fixture chain for ordered data creation.

## Definition of Done
- `pytest tests/unit/ -m unit` runs with no external dependencies.
- Real-service fixtures connect to Postgres/Qdrant/Redis/Neo4j and roll back per test.
- Seed factory fixtures create ordered data (user → hub → workflow).
