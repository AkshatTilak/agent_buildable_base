# Subtask 01_02: Root conftest.py — Markers & Real-Service Fixtures

## Parent Base Task
`tasks/v8/base/01_test_infrastructure_foundation.md`

## Objective
Create the master pytest conftest that loads `.env.test`, registers markers, and provides real-service fixtures connecting to actual development services.

## Tasks
1. [x] Load `.env.test` and provide a `test_settings` fixture.
2. [x] Register markers: `unit`, `integration`, `e2e`, `live_api`, `streaming`, `performance`.
3. [x] Provide `real_db_session` async fixture connecting to actual running Postgres.
4. [x] Provide `qdrant_client`, `redis_client`, `neo4j_driver` fixtures connected to actual running services.
5. [x] Provide `gateway_client` fixture (httpx `AsyncClient` against running gateway on `:8000` or TestClient-mounted gateway).
6. [x] Provide `seed_user`, `seed_hub`, `seed_workflow` factory fixture chain for ordered data creation with unique identifiers (deleting test data is not necessary).

## Definition of Done
- `pytest tests/unit/ -m unit` runs with no external dependencies.
- Real-service fixtures connect to Postgres/Qdrant/Redis/Neo4j successfully.
- Seed factory fixtures create ordered data (user → hub → workflow) without requiring post-test data deletion.
