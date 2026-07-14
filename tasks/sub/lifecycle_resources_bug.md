# Subtask: Docker-Compose Resources Bug Fix

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Fix the duplicated resources reservation key in the docker-compose YAML configuration.
- [x] Properly specify nvidia driver GPU capabilities reservation inside `deploy.resources`.

## Dependencies
- docker-compose environment with NVIDIA container toolkit.

## Definition of Done
- running `docker compose config` evaluates without resource duplication parsing exceptions.
