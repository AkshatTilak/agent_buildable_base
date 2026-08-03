# Subtask 01_05: Auth UI Overhaul & Account State Views

## Tasks
1. Refactor `frontend/src/components/auth/LoginPage.tsx` to handle distinct error responses for `ACCOUNT_SUSPENDED`, `ACCOUNT_SOFT_DELETED`, `ACCOUNT_REJECTED`, and `ACCOUNT_PENDING_APPROVAL`.
2. Add a "Delete Account" self-service modal in `frontend/src/components/SettingsPage.tsx` or user profile header with confirmation step.
3. Update `AdminConsole.tsx` / `UserDirectory.tsx` to render soft-deleted users with a distinct badge and offer a hard purge action.
4. Ensure smooth logout redirects to `/login` with clear toast notification.
