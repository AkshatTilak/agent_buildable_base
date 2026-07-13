# ST03_02: Implement Auth API Endpoints

## Parent Link
`base/BT03_auth_and_rbac.md`

## Actionable Steps
- [ ] Step 1: Implement POST /register with email/password validation, password hashing, DB insert
- [ ] Step 2: Implement POST /login with credential verification and JWT issuance
- [ ] Step 3: Implement GET /me with JWT dependency injection and profile retrieval
- [ ] Step 4: Implement OAuth2PasswordBearer dependency for token extraction
- [ ] Step 5: Write API tests for register, login, and profile endpoints

## Dependencies
- `ST03_01_security_utils.md` — Security utils must be implemented
- `ST02_04_db_session.md` — DB session must be functional

## Definition of Done
All auth endpoints functional, JWT flow verified end-to-end, API tests passing.
