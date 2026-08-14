# Subtask 16_02: Provider Credentials Real Integration Tests

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `tests/integration/gateway/test_credentials_real.py` — provider credentials.

## Tasks
1. [ ] Set Google API key via API → verify encrypted in DB → verify `mask_key` in response.
2. [ ] Override env-based key with DB key → verify DB takes precedence.
3. [ ] Delete DB key → verify falls back to env.
4. [ ] List all provider slots (env + DB merged view).

## Definition of Done
- Provider credentials verified against actual running Postgres (`:5432`) and Gateway (`:8000`) (set/encrypt/mask, DB precedence, env fallback, merged view).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
