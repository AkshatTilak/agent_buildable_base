# BT01: Infrastructure & Docker Compose Setup

## Objective
Stand up the complete containerized microservices topology with all 9 services communicating on a shared Docker network.

## Business/System Value
The entire system depends on Docker Compose for local development parity with production. No service can be developed or tested without this foundation.

## Subtask Registry
- [ ] `sub/ST01_01_docker_compose_core.md` — Validate & refine docker-compose.yml (PostgreSQL, Redis, Kafka)
- [ ] `sub/ST01_02_docker_compose_ai.md` — Validate & configure AI services (Langfuse, Langflow, RAGFlow)
- [ ] `sub/ST01_03_docker_compose_spark.md` — Validate Spark master/worker configuration
- [ ] `sub/ST01_04_dockerfile_app.md` — Finalize multi-stage Dockerfile for the FastAPI app

## Complexity Rating
**Medium** — Configuration-heavy, but no complex application logic.

## Source Code Location
`../../echomind-core/docker-compose.yml`, `../../echomind-core/Dockerfile`
