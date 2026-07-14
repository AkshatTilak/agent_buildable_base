# Subtask: PII Handling

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [ ] Implement PII scanning for API responses (post-flight output validation).
- [ ] Implement PII redaction in structured log messages.
- [ ] Strip PII from Kafka trace payloads before publishing.
- [ ] Integrate with EvalOps PII leakage detection tests.

## Dependencies
- GuardRoute post-flight pipeline. Kafka producer. Logging framework.

## Definition of Done
- PII not leaked in responses, logs, or trace payloads. EvalOps PII tests pass.
