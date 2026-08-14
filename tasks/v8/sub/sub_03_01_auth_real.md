# Subtask 03_01: Auth Real Integration Tests

## Parent Base Task
`tasks/v8/base/03_auth_user_lifecycle_real.md`

## Objective
Create `tests/integration/gateway/test_auth_real.py` — full auth lifecycle against real Postgres.

## Tasks
1. [ ] Self-registration with email/password → user created in real Postgres.
2. [ ] Login → JWT token issued → validate token contents.
3. [ ] Protected route access with valid/invalid/expired tokens.
4. [ ] Password change, account lockout after N failures, unlock.
5. [ ] Admin approval gate (when `AUTO_APPROVE_EMAIL_DOMAINS` is empty).
6. [ ] OAuth callback simulation (Google/GitHub identity linking).

## Definition of Done
- All auth lifecycle tests pass against actual running Postgres (`:5432`) and Gateway (`:8000`).
- JWT tokens validated for contents and expiry.
- Lockout, approval gate, and OAuth linking verified.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend auth code.
- Deleting test data is not necessary.
