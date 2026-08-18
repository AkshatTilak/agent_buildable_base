---
version: 1.0.0
updated: 2026-08-18
id: skill_qa_backend
links:
  - ../../../references/tests/tests.md
  - ../../../references/db/db.md
  - ../../../workflows/execution/work_verification.md
  - ../../../workflows/quality/fallback_policy.md
---

# Skill: QA — Backend

## Trigger Conditions
- Load this skill when auditing or testing backend systems.
- Load when verifying API routes, data models, or business logic.

## Steps
1. **Verify type safety**: models are strictly typed and schema/models/migrations
   are in sync (see `../../../references/db/`).
2. **Verify error handling**: errors are handled with intent — no blanket
   `try/except`, no hard fallbacks (see
   `../../../workflows/quality/fallback_policy.md`).
3. **Run both test tracks**: unit + real system interaction (see
   `../../../references/tests/`).
4. **Verify edge cases**: empty/zero, boundary, invalid input, concurrency,
   failure paths.
5. **Verify observability**: logging/tracing is present and not bloated (see
   `../../../references/logs/`).

## Expected Outputs
- Backend verified against the Definition of Done.
- Both test tracks pass.
- Edge cases covered.

## References
- `../../../references/tests/tests.md`
- `../../../references/db/db.md`
