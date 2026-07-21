# Base Task: Docker Infrastructure — 16GB RAM Optimization

## Objective
Optimize the Docker Compose infrastructure to work reliably on a 16GB RAM development machine by adding memory limits, creating lightweight dev profiles, and making heavy services (Neo4j, Kafka) optional.

## Business/System Value
The current docker-compose spins up 8+ services with no memory limits — Postgres, Qdrant, Neo4j, Redis, Zookeeper, Kafka, gateway, inference. On a 16GB RAM machine this can consume 6-8GB+ leaving insufficient memory for the gateway, inference, frontend dev server, and IDE. V3 creates tiered profiles so developers can run only what they need.

## Complexity Rating
Medium (Docker Compose configuration, no application logic changes).

## Subtask Registry
* `[x] sub/docker_memory_limits.md` — Add `mem_limit` to all services with budgets tuned for 16GB total host RAM.
* `[x] sub/docker_dev_profiles.md` — Create `core` profile (Postgres+Qdrant+Redis ~1.3GB), make Neo4j/Kafka/Zookeeper profile-gated.
* `[x] sub/docker_dev_override.md` — Create `docker-compose.dev.yml` override with hot-reload mounts, reduced logging, and minimal Kafka config.
* `[x] sub/docker_inference_cpu_mode.md` — Make inference GPU reservation optional, add CPU-mode fallback for non-GPU systems.
