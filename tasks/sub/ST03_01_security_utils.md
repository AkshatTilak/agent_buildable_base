# ST03_01: Implement Security Utilities

## Parent Link
`base/BT03_auth_and_rbac.md`

## Actionable Steps
- [x] Step 1: Implement `hash_password()` using passlib bcrypt CryptContext
- [x] Step 2: Implement `verify_password()` for credential validation
- [x] Step 3: Implement `create_access_token()` JWT generation with python-jose
- [x] Step 4: Implement `decode_access_token()` JWT validation with error handling
- [x] Step 5: Implement `get_current_user` FastAPI dependency (OAuth2PasswordBearer)

## Dependencies
- `python-jose[cryptography]`, `passlib[bcrypt]` installed ✅

## Definition of Done
All security functions implemented, tested, and type-annotated. JWT creation/validation verified.

## Completion Notes
Implemented 2026-07-14 (`app/common/security.py`):
- `CryptContext(schemes=["bcrypt"])` for password hashing ✅
- `create_access_token()`: encodes sub, email, role, exp, iat ✅
- `decode_access_token()`: raises `AuthenticationError` on JWTError ✅
- `oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/login")` ✅
- `get_current_user` dependency returns decoded payload dict ✅
