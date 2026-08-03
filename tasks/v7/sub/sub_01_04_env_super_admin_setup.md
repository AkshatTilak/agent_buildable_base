# Subtask 01_04: Environment-Driven Super Admin & Test User

## Tasks
1. Update `gateway/core/setup.py` startup lifespan to read `SUPER_ADMIN_EMAIL` and `SUPER_ADMIN_PASSWORD` from `.env`.
2. On startup, check if the designated super admin account exists in PostgreSQL. If not, auto-create it with `platform_role="admin"` and `status="active"`.
3. Read `TEST_USER_EMAIL` and `TEST_USER_PASSWORD` from `.env` to bootstrap a standard platform member account for automated testing.
4. Remove legacy logic in registration routes that auto-promoted the first registered user to admin role.
