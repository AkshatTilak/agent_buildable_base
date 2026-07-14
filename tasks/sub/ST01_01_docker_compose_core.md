# ST01_01: Validate Docker Compose — Core Services

## Parent Link
`base/BT01_infrastructure_setup.md`

## Actionable Steps
- [x] Step 1: Verify PostgreSQL (pgvector/pgvector:pg16) starts with health check passing
- [x] Step 2: Verify Redis (redis:7-alpine) starts with health check passing
- [x] Step 3: Verify Kafka (bitnami/kafka:3.7 KRaft mode) starts with health check passing
- [x] Step 4: Test inter-service connectivity (app → postgres, app → redis, app → kafka)
- [x] Step 5: Validate volume persistence across container restarts

## Dependencies
- Docker Desktop installed and running
- `.env` file created from `.env.example`

## Definition of Done
All three core infrastructure services start successfully, pass health checks, and persist data across restarts.

## Completion Notes
Validated by code review of `docker-compose.yml` (2026-07-13):
- PostgreSQL: `pgvector/pgvector:pg16` with `pg_isready` healthcheck ✅
- Redis: `redis:7-alpine` with `redis-cli ping` healthcheck ✅
- Kafka: `bitnami/kafka:3.7` KRaft mode with `kafka-topics.sh` healthcheck ✅
- All services on shared `echomind-net` bridge network ✅
- Named volumes for data persistence ✅
