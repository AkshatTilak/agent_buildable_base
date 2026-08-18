---
version: 1.0.0
updated: 2026-08-18
id: skill_qa_frontend
links:
  - ../../../references/tests/tests.md
  - ../../../design/ux/
  - ../../../workflows/execution/work_verification.md
---

# Skill: QA — Frontend

## Trigger Conditions
- Load this skill when auditing or testing frontend systems.
- Load when verifying UI behavior, accessibility, or responsiveness.

## Steps
1. **Verify states**: every screen/component defines empty/zero, populated, and
   errored states (see `../../../CONVENTIONS.md` §2).
2. **Verify UX flows**: user flows match the design in `../../../design/ux/`.
3. **Run both test tracks**: unit + real system interaction (see
   `../../../references/tests/`).
4. **Verify edge cases**: empty data, long content, slow network, error states.
5. **Verify accessibility** and responsiveness where applicable.

## Expected Outputs
- Frontend verified against the Definition of Done.
- Both test tracks pass.
- All UI states covered.

## References
- `../../../references/tests/tests.md`
- `../../../design/design.md`
