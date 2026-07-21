# Subtask: Create Reusable Shared Components

**Parent Link:** `base/02_frontend_design_system.md`

## Actionable Steps
- [x] Create `frontend/src/components/shared/` directory.
- [x] Create `StatusBadge.tsx` — pill-shaped badge with variant props (success/warning/error/info). Uses soft accent backgrounds.
- [x] Create `LoadingSkeleton.tsx` — shimmer skeleton with variant props (card, row, chart, text). Uses `shimmer` keyframe.
- [x] Create `ConfirmModal.tsx` — themed confirmation dialog (already specified in `fix_frontend_confirm_modal.md`).
- [x] Create `Toast.tsx` and `ToastProvider.tsx` — global toast notification system with success/error/warning/info variants.
  - Position: bottom-right, stacked.
  - Auto-dismiss: 5 seconds with progress indicator.
  - Animation: slide-in-right, slide-out-right.
  - Must be context-based (`useToast()` hook) for any component to trigger.
- [x] Create `EmptyState.tsx` — reusable empty state with icon, title, description, and optional action button.
- [x] All shared components must use design tokens exclusively — zero hardcoded colors.

## Dependencies
- `sub/design_css_tokens.md` must be completed first.

## Definition of Done
- `shared/` directory contains StatusBadge, LoadingSkeleton, ConfirmModal, Toast/ToastProvider, EmptyState.
- Each component has proper TypeScript props interfaces.
- Components build without errors.
