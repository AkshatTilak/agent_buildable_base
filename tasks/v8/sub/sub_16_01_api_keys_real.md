# Subtask 16_01: API Keys Real Integration Tests

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `tests/integration/gateway/test_api_keys_real.py` — API key lifecycle.

## Tasks
1. [x] Create key → verify hashed key in DB → use key for auth → verify usage count increments.
2. [x] Hub-scoped key (only works for requests within that hub).
3. [x] Revoke key → verify auth fails.
4. [x] Rate limit enforcement per key.
5. [x] Key stats/analytics endpoint.

## Definition of Done
- API key lifecycle verified against actual running Postgres (`:5432`) and Gateway (`:8000`) (create, use, revoke, rate limit, stats).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
