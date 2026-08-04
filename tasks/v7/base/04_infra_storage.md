# Base Task 4: Infrastructure & Persistent Storage Alignment

## What
Update Docker compose configuration to bind PostgreSQL, Redis, Qdrant, and Neo4j data directories directly to local host paths (`./data/*`) instead of Docker named volumes.

## Why
Using Docker named volumes caused divergence between local development states and Docker container states. Local host path mapping guarantees consistent data persistence across both execution models.

## Associated Subtasks
- `[x]` `tasks/v7/sub/sub_04_01_docker_local_host_paths.md`
