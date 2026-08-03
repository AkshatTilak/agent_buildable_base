# Subtask 01_02: User APIs, Soft Delete & Admin Hard Purge

## Tasks
1. Add self-service soft deletion endpoint `DELETE /auth/me` and `DELETE /users/me` allowing active users to soft-delete their own account.
2. Update `DELETE /admin/users/{id}` in `gateway/api/admin_users.py`:
   - Soft-delete by default if user is active.
   - If `hard=true` query param or user is already soft-deleted, perform a permanent hard delete from PostgreSQL.
3. Update `POST /admin/users/{id}/suspend` and `POST /admin/users/{id}/reject` to immediately revoke session tokens and write audit log entries.
4. Ensure proper HTTP status codes (200 OK for soft delete, 204 No Content for hard delete).
