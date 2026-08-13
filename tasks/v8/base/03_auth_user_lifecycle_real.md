# Base Task 3: Real-World Integration Tests — Auth & User Lifecycle (v8)

## Objective
Test the full auth system against real Postgres — registration, login, JWT issuance, session management, password reset, invite flows, and RBAC enforcement.

## Why
Existing auth tests are mock-based and never validate that the auth system works against a real database with real password hashing, JWT signing, session storage, and RBAC queries.

## Scope
- **`tests/integration/gateway/test_auth_real.py`** — Full auth lifecycle:
  - Self-registration with email/password → user created in real Postgres.
  - Login → JWT token issued → validate token contents.
  - Protected route access with valid/invalid/expired tokens.
  - Password change, account lockout after N failures, unlock.
  - Admin approval gate (when `AUTO_APPROVE_EMAIL_DOMAINS` is empty).
  - OAuth callback simulation (Google/GitHub identity linking).
- **`tests/integration/gateway/test_invites_real.py`** — Invite system:
  - Admin creates invite → invite row in DB → email payload generated.
  - Invite acceptance → user account created with correct hub membership.
  - Invite expiry (TTL enforcement).
  - Duplicate invite handling.
- **`tests/integration/gateway/test_rbac_real.py`** — Role enforcement:
  - Admin vs member vs viewer permissions across all hub operations.
  - API key authentication (create key → use key → revoke key).
  - Rate limiting enforcement with real SlowAPI against real endpoints.

## Associated Subtasks
1. `[x]` `sub_03_01_auth_real.md`: `test_auth_real.py` — full auth lifecycle against real Postgres.
2. `[x]` `sub_03_02_invites_real.md`: `test_invites_real.py` — invite system against real Postgres.
3. `[x]` `sub_03_03_rbac_real.md`: `test_rbac_real.py` — role enforcement, API keys, rate limiting.


## Definition of Done
- All auth lifecycle, invite, and RBAC tests pass against real Postgres.
- JWT tokens validated for contents and expiry.
- Lockout, approval gate, and OAuth linking verified.
- API key create/use/revoke and rate limiting verified against real endpoints.
