# Subtask: Secure Logout API & Auth Routes

- Create `/auth/logout` endpoint in `gateway/auth/routes.py` to invalidate tokens and clear cookies.
- Update frontend `authSlice.ts` to call this endpoint instead of just clearing local storage.
