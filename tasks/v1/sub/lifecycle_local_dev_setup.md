# Subtask: Local Development Setup

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Document minimal setup: run infra via Docker, gateway/inference natively.
- [x] Support selective service startup (e.g., just Postgres + Qdrant).
- [x] Gateway gracefully handles missing services (log warnings, skip functionality).
- [x] Hot reload: `--reload` when `APP_ENV=development` for gateway and inference.
- [x] Frontend: `npm run dev` with HMR.

## Dependencies
- docker-compose configuration. Gateway graceful degradation.

## Definition of Done
- Developer can run platform with minimal Docker services. Missing services don't crash.
