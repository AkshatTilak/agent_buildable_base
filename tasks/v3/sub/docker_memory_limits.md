# Subtask: Docker Memory Limits Optimization

**Parent Link:** `base/07_docker_infrastructure_ram.md`

## Actionable Steps
- [x] In `infrastructure/docker-compose.yml`, add strict `deploy.resources.limits.memory` settings to all services:
  - `postgres`: `512m`
  - `qdrant`: `512m`
  - `redis`: `256m`
  - `neo4j`: `768m`
  - `kafka`: `512m`
  - `zookeeper`: `256m`
  - `gateway`: `512m`
  - `inference`: `2048m` (CPU fallback mode limit)
- [x] Set `restart: unless-stopped` on all infrastructure services to ensure resilience.
- [x] Add container healthchecks with reasonable timeouts and retries for postgres, qdrant, and redis.

## Dependencies
- None.

## Definition of Done
- All services in `docker-compose.yml` have explicit memory limits.
- Total memory footprint across all default containers stays well under 4GB RAM.
