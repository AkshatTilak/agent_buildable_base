# Subtask: GuardRoute Code Execution Sandbox

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] AST-level Python execution sandboxing via `RestrictedPython`.
- [x] Block file, subprocess, network, and import/eval operations.
- [x] Timeout: 10s. Enforce memory protections.

## Definition of Done
- Sandbox blocks dangerous statements and captures script output stdout/stderr.
