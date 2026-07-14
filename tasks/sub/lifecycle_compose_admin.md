# Subtask: Docker-Compose Admin Profile

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Add `pgadmin` and `kafka-ui` services to `docker-compose.yml`.
- [x] Lock them behind the `--profile admin` profile.

## Dependencies
- docker compose profiles functionality.

## Definition of Done
- Admin tools only spin up when explicitly running docker compose with `--profile admin`.
