# Subtask 16_04: Model Registry Real Integration Tests

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `tests/integration/gateway/test_models_real.py` — model registry.

## Tasks
1. [x] List available models (LiteLLM + local) → verify response schema.
2. [x] Register custom model → verify DB row.
3. [x] Select model for role → verify active model changes.
4. [x] Delete/download model endpoints.
5. [x] Test model health probe.

## Definition of Done
- Model registry verified against actual running Gateway (`:8000`) and Postgres (`:5432`) (list, register, select, delete/download, health probe).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
