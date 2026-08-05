# Subtask: Environment-driven Super Admin & Test User

- Modify `gateway/core/setup.py` (or equivalent initialization hook) to read `ADMIN_EMAIL`, `ADMIN_PASSWORD` and optionally `TEST_USER` credentials from `.env`.
- Remove legacy logic where the first registered user is automatically promoted to admin.
- Ensure the super admin is created seamlessly on the first startup if they don't exist in the database.
