# Subtask: AgentHub Premium UI Overhaul

**Parent Link:** `base/03_frontend_premium_ui.md`

## Actionable Steps
- [ ] Add search/filter bar at the top — filter agents by name, role, or model.
- [ ] Add agent status badges on cards (active/inactive) using `StatusBadge` shared component.
- [ ] Add agent analytics preview on each card (placeholder data: total queries, avg response time).
- [ ] Add subtle hover effect on agent cards: `scale(1.01)`, `border-color` transition to accent.
- [ ] Replace browser `confirm()` with `ConfirmModal` for delete (coordinated with `fix_frontend_confirm_modal.md`).
- [ ] Add toast notifications for all CRUD operations (create/update/delete success/failure).
- [ ] Use `EmptyState` shared component for zero-agents state with illustration.
- [ ] Add agent count badge next to section header.
- [ ] Improve modal styling to match design system (proper header/footer separators, blur overlay).

## Dependencies
- `sub/design_shared_components.md` (for StatusBadge, ConfirmModal, EmptyState, Toast).

## Definition of Done
- Agent cards have search/filter, status badges, hover animations.
- All CRUD operations show toast notifications.
- Delete uses themed confirm modal.
- Empty state shows meaningful illustration.
