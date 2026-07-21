# Subtask: Replace Browser confirm() with Themed Confirm Modal

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [x] Create `frontend/src/components/shared/ConfirmModal.tsx` — themed modal with title, message, cancel/confirm buttons, danger variant.
- [x] Must follow the design system: `var(--bg-surface)`, `var(--radius-xl)`, blur overlay, rose accent for destructive.
- [x] `AgentHub.tsx:100` — Replace `if (!confirm(...))` with `ConfirmModal` invocation. Show agent name in the modal body.
- [x] Audit all other components for `confirm()` or `alert()` calls and replace them.
- [x] ConfirmModal must support both `Promise`-based and callback usage patterns.
- [x] Add keyboard support: Enter to confirm, Escape to cancel.

## Dependencies
- Design system tokens (`sub/design_css_tokens.md`).

## Definition of Done
- Zero instances of `confirm()` or `alert()` in the frontend source.
- Confirm modal matches design system tokens.
- Keyboard accessible (Enter/Escape).
