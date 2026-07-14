# Subtask: Security CORS Configuration

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Replace `allow_origins=["*"]` with a configurable allowlist.
- [x] Default to `["http://localhost:3000", "http://localhost:5173"]`.
- [x] Read from `CORS_ORIGINS` environment variable.

## Dependencies
- FastAPI CORS Middleware.

## Definition of Done
- CORS headers correctly enforce origin matching against the configured allowlist.
