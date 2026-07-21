# Subtask: Docker Dev Profiles Setup

**Parent Link:** `base/07_docker_infrastructure_ram.md`

## Actionable Steps
- [ ] Group services in `infrastructure/docker-compose.yml` into logical execution profiles:
  - `profiles: ["core"]` for `postgres`, `qdrant`, `redis` (Essential DB services — ~1.3GB total RAM)
  - `profiles: ["full"]` or `profiles: ["messaging"]` for `kafka`, `zookeeper` (~768MB RAM)
  - `profiles: ["graph"]` for `neo4j` (~768MB RAM)
  - `profiles: ["app"]` for `gateway`, `inference`
- [ ] Allow running lightweight stack using `docker compose --profile core up -d`.
- [ ] Document profile options in `README.md` and `references/deployment/infrastructure.md`.

## Dependencies
- None.

## Definition of Done
- `docker compose --profile core up -d` boots only postgres, qdrant, and redis.
- Developers on 16GB machines can run core DBs in Docker and app servers natively.
