# Subtask 03_03: RBAC Real Integration Tests

## Parent Base Task
`tasks/v8/base/03_auth_user_lifecycle_real.md`

## Objective
Create `tests/integration/gateway/test_rbac_real.py` — role enforcement, API keys, and rate limiting.

## Tasks
1. [ ] Admin vs member vs viewer permissions across all hub operations.
2. [ ] API key authentication (create key → use key → revoke key).
3. [ ] Rate limiting enforcement with real SlowAPI against real endpoints.

## Definition of Done
- Role-based permissions verified across hub operations against actual running Postgres (`:5432`) and Gateway (`:8000`).
- API key create/use/revoke verified.
- Rate limiting enforced with real SlowAPI against real endpoints.
- If errors arise, inspect Docker container logs (`docker compose logs db redis`) and fix root causes directly in backend RBAC code.
- Deleting test data is not necessary.
