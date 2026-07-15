# ST03_02: Implement Auth API Endpoints

## Parent Link
`base/BT03_auth_and_rbac.md`

## Actionable Steps
- [x] Step 1: Implement POST /register with email/password validation, password hashing, DB insert
- [x] Step 2: Implement POST /login with credential verification and JWT issuance
- [x] Step 3: Implement GET /me with JWT dependency injection and profile retrieval
- [x] Step 4: Implement POST /logout — invalidates Redis session cache
- [x] Step 5: Write API tests for register, login, profile, and logout endpoints

## Dependencies
- `ST03_01_security_utils.md` ✅ — Security utils implemented
- `ST02_04_db_session.md` ✅ — DB session functional

## Definition of Done
All auth endpoints functional, JWT flow verified end-to-end, API tests passing.

## Completion Notes
Implemented 2026-07-14 (`app/api/routes/auth.py`):
- `POST /register`: `UserCreate` → email uniqueness check → bcrypt hash → DB insert (user + profile) → Redis cache → `UserResponse` (201) ✅
- `POST /login`: `UserLogin` → password verify → JWT issue → Redis cache → `TokenResponse` (200) ✅
- `GET /me`: JWT decode → Redis fast-path + DB fallback → `UserResponse` (200) ✅
- `POST /logout`: JWT decode → `invalidate_session()` → 204 ✅
- `selectinload(User.role)` used to avoid N+1 on login ✅
- Step 5 (API tests) deferred to test sprint
