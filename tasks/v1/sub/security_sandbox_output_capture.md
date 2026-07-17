# Subtask: Security Sandbox Output Capture

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Capture stdout and stderr from sandboxed execution.
- [x] Return output as structured payload in `SubAgentResult` schema.
- [x] Truncate output to a maximum of 10,000 characters.

## Dependencies
- RestrictedPython, context redirect managers.

## Definition of Done
- Sandbox captures both stdout and stderr and returns structured response records truncated cleanly.
