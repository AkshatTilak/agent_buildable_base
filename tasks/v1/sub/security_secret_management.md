# Subtask: Secret Management

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Verify `.env` is in `.gitignore`.
- [x] Create `.env.example` with placeholder values only — never real keys.
- [x] Document Docker secrets or cloud secret manager integration for production.
- [x] Ensure API keys, DB passwords, provider keys never appear in logs.

## Dependencies
- `.gitignore` file. Logging framework in `common/observability/`.

## Definition of Done
- `.env` excluded from git. `.env.example` provided. Secrets redacted from logs.
