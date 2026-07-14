# Subtask: Audit & Compliance Logging

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Log all API requests (method, path, status code, latency) to structured logs.
- [x] Do NOT log request/response bodies by default (PII risk). Optional in dev mode.
- [x] Log authentication failures (invalid API keys).
- [x] Log rate limit violations.
- [x] Log prompt injection detection triggers.
- [x] Log sandbox execution attempts and outcomes.

## Dependencies
- Logging framework in `common/observability/`. Auth middleware. Rate limiter.

## Definition of Done
- All security events logged. Body logging disabled by default. Structured log format.
