---
version: 1.0.0
updated: 2026-08-18
id: skill_traceback_check
links:
  - ../../../workflows/execution/work_verification.md
  - ../../../references/logs/logs.md
  - ../../../workflows/quality/fallback_policy.md
---

# Skill: Traceback Check

## Trigger Conditions
- Load this skill when a traceback, exception, or error log needs diagnosis.
- Load when a task fails and the failure is logged in `../../../references/logs/`.

## Steps
1. **Read the full traceback** from the failure log (see
   `../../../references/logs/`).
2. **Identify the root cause**: the first frame where the error originates, not
   the last.
3. **Check the stack**: confirm the error is not a version/dependency mismatch
   (see `../../../STACK.md`).
4. **Reproduce** the error in isolation if possible.
5. **Fix the root cause** — do not mask it with a blanket `try/except` (see
   `../../../workflows/quality/fallback_policy.md`).
6. **Add a regression test** for the failure (see `../../../references/tests/`).

## Expected Outputs
- Root cause identified and fixed.
- Regression test added.
- Failure logged and traceable.

## References
- `../../../references/logs/logs.md`
- `../../../workflows/quality/fallback_policy.md`
