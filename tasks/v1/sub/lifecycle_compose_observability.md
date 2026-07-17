# Subtask: Docker-Compose Observability Profile

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Add `jaeger` and `otel-collector` services to `docker-compose.yml`.
- [x] Lock them behind the `--profile observability` profile.

## Dependencies
- OpenTelemetry collector configuration.

## Definition of Done
- Tracing collector and Jaeger UI only start when explicitly running docker compose with `--profile observability`.
