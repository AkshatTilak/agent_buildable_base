# Subtask 03_02: Invites Real Integration Tests

## Parent Base Task
`tasks/v8/base/03_auth_user_lifecycle_real.md`

## Objective
Create `tests/integration/gateway/test_invites_real.py` — invite system against real Postgres.

## Tasks
1. [ ] Admin creates invite → invite row in DB → email payload generated.
2. [ ] Invite acceptance → user account created with correct hub membership.
3. [ ] Invite expiry (TTL enforcement).
4. [ ] Duplicate invite handling.

## Definition of Done
- Invite creation, acceptance, expiry, and duplicate handling verified against actual running Postgres (`:5432`).
- Accepted invite creates user with correct hub membership.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend invite logic.
- Deleting test data is not necessary.
