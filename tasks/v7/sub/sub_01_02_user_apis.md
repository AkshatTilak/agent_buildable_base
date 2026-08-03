# Subtask: User APIs (Hard/Soft Delete & Suspend)

- Update `gateway/api/admin_users.py` to allow admins to perform hard deletions.
- Add `DELETE /users/me` for users to soft delete their accounts.
- Refine the `suspend_user` endpoint to immediately revoke all active tokens/sessions.
