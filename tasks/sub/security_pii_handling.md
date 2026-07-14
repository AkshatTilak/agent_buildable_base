# Subtask: PII Handling

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Implement PII scanning for API responses (post-flight output validation).
- [x] Implement PII redaction in structured log messages.
- [x] Strip PII from Kafka trace payloads before publishing.
- [x] Integrate with EvalOps PII leakage detection tests.

## Dependencies
- GuardRoute post-flight pipeline. Kafka producer. Logging framework.

## Definition of Done
- PII not leaked in responses, logs, or trace payloads. EvalOps PII tests pass.
