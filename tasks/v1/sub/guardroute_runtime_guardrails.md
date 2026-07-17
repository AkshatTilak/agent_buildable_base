# Subtask: GuardRoute Runtime Guardrails

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] Pre-flight: prompt injection scanner and system prompt override blockers.
- [x] Post-flight: scan output for PII (emails, phones, API keys) and redact, and enforce toxicity check (threshold 0.1).

## Definition of Done
- Prompt overrides are blocked on input, and generated output is filtered for PII.
