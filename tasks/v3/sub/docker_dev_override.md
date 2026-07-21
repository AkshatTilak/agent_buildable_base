# Subtask: Docker Dev Override File Creation

**Parent Link:** `base/07_docker_infrastructure_ram.md`

## Actionable Steps
- [x] Create `infrastructure/docker-compose.dev.yml` override file:
  - Mount local source directories into `gateway` for uvicorn hot-reloading.
  - Set `LOG_LEVEL=DEBUG` for dev container outputs.
  - Configure minimal logging drivers (`json-file` with `max-size: 10m`, `max-file: 3`) to prevent disk fill.
- [x] Provide command instructions for launching dev mode: `docker compose -f infrastructure/docker-compose.yml -f infrastructure/docker-compose.dev.yml up`.

## Dependencies
- None.

## Definition of Done
- `docker-compose.dev.yml` exists and validates with `docker compose config`.
- Log retention limits prevent container logs from bloating disk space.
