# Subtask: Audit & Compliance Logging

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [ ] Log all API requests (method, path, status code, latency) to structured logs.
- [ ] Do NOT log request/response bodies by default (PII risk). Optional in dev mode.
- [ ] Log authentication failures (invalid API keys).
- [ ] Log rate limit violations.
- [ ] Log prompt injection detection triggers.
- [ ] Log sandbox execution attempts and outcomes.

## Dependencies
- Logging framework in `common/observability/`. Auth middleware. Rate limiter.

## Definition of Done
- All security events logged. Body logging disabled by default. Structured log format.
