# Base Task 1: Auth & User Lifecycle Management

## What
Complete the authentication and user lifecycle management system by introducing soft/hard deletion, secure session revocation, environment-driven super admin setup, and dedicated auth state views in the UI.

## Why
v6 introduced basic user management but lacked account deletion, session invalidation on logout, and secure admin bootstrapping. Relying on auto-promoting the first user to admin creates security risks, and soft deletion is required for proper auditing and compliance.

## Associated Subtasks
- `[ ]` `tasks/v7/sub/sub_01_01_soft_delete_models.md`
- `[ ]` `tasks/v7/sub/sub_01_02_user_apis_and_deletion.md`
- `[ ]` `tasks/v7/sub/sub_01_03_logout_and_session_revocation.md`
- `[ ]` `tasks/v7/sub/sub_01_04_env_super_admin_setup.md`
- `[ ]` `tasks/v7/sub/sub_01_05_auth_ui_overhaul.md`
