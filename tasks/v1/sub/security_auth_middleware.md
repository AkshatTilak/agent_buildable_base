# Subtask: Security Authentication Middleware

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Implement API key validation middleware on the gateway.
- [x] API keys passed via `X-API-Key` header.
- [x] Toggle authentication via `AUTH_ENABLED=false` / `AUTH_ENABLED=true`.

## Dependencies
- FastAPI middleware framework.

## Definition of Done
- Gateway rejects requests without valid API keys with `401 Unauthorized` when `AUTH_ENABLED=true`.
