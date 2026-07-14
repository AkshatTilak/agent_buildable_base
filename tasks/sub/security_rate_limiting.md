# Subtask: Security Rate Limiting

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Apply rate limiting using `slowapi` or `fastapi-limiter`.
- [x] Set limits: General (100 req/min), Inference (30 req/min), Uploads (10 req/min).
- [x] Return `429 Too Many Requests` with `Retry-After` header.

## Dependencies
- `slowapi` package, Redis client for distributed state.

## Definition of Done
- Gateway successfully rate limits clients and returns `429` responses with retry guidance.
