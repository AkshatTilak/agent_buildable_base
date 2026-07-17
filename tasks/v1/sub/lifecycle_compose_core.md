# Subtask: Docker-Compose Core Services

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Add PostgreSQL, Qdrant, Neo4j, Redis, Kafka + ZooKeeper, Gateway, and Inference services to `docker-compose.yml` under default profile.
- [x] Map correct ports, dependency indicators, healthchecks, and volume paths.

## Dependencies
- Individual service container configurations.

## Definition of Done
- Running `docker compose up` starts all core platform components and links them securely.
