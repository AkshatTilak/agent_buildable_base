# Subtask: Common Configuration Validation

**Parent Link:** `base/common_library.md`

## Actionable Steps
- [x] Fail-fast if required environment variables are missing (e.g. `DATABASE_URL`).
- [x] Warn for optional but recommended variables (e.g. `GOOGLE_API_KEY`).
- [x] Validate `ACTIVE_PROJECTS` entries against available directories.

## Definition of Done
- Validation rules check config on startup and crash/warn correctly.
