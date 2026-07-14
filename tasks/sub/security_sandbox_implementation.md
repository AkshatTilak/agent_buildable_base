# Subtask: Security Sandbox Implementation

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Use `RestrictedPython` for AST-level sandboxing.
- [x] Block file I/O, subprocesses, network access, `__import__`, `eval`, and `exec`.
- [x] Set execution timeout to 10 seconds.
- [x] Enforce memory limits.

## Dependencies
- `RestrictedPython` library.

## Definition of Done
- Python execution sandbox blocks dangerous functions and times out on loops.
