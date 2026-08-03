# Subtask 01_03: Secure Logout API & Session Revocation

## Tasks
1. Implement `POST /auth/logout` endpoint in `gateway/auth/routes.py`.
2. Delete active `UserSession` from PostgreSQL, clear session cookies (`contained_session`, `auth_token`), and add the JWT token ID to invalidation store.
3. Update `frontend/src/store/authSlice.ts` `logout` function to trigger `api.logout()`, await response, and purge all localStorage state.
4. Add automated test verifying logged-out tokens return `401 Unauthorized` on subsequent calls.
