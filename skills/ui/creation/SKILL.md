---
version: 1.0.0
updated: 2026-08-18
id: skill_ui_ux_creation
links:
  - ../../../design/ux/
  - ../../../CONVENTIONS.md
  - ../../../workflows/user/user_input.md
---

# Skill: UI/UX Creation

## Trigger Conditions
- Load this skill when designing or building user interfaces.
- Load when creating UX flows or screens.

## Steps
1. **Clarify** the user interaction with the user (see
   `../../../workflows/user/user_input.md`).
2. **Design the UX** in `../../../design/ux/` before building (see
   `../../../design/design.md`).
3. **Define states** for every screen/component: empty/zero, populated, errored
   (see `../../../CONVENTIONS.md` §2).
4. **Build** the UI following the design and the project's stack
   (`../../../STACK.md`).
5. **Test** both tracks: unit + real system interaction (see
   `../../../references/tests/`).

## Expected Outputs
- UX design in `../../../design/ux/`.
- UI implementation with all states defined.
- Tests covering the UI flows and edge cases.

## References
- `../../../design/design.md`
- `../../../CONVENTIONS.md`
