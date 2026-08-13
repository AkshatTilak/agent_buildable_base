# Subtask 16_01: API Keys Real Integration Tests

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `tests/integration/gateway/test_api_keys_real.py` — API key lifecycle.

## Tasks
1. [ ] Create key → verify hashed key in DB → use key for auth → verify usage count increments.
2. [ ] Hub-scoped key (only works for requests within that hub).
3. [ ] Revoke key → verify auth fails.
4. [ ] Rate limit enforcement per key.
5. [ ] Key stats/analytics endpoint.

## Definition of Done
- API key lifecycle verified against real Postgres (create, use, revoke, rate limit, stats).
