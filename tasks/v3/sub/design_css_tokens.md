# Subtask: Implement CSS Design Token System

**Parent Link:** `base/02_frontend_design_system.md`

## Actionable Steps
- [ ] Rewrite `frontend/src/index.css` to implement the full token system from `references/structure/design_system.md`:
  - Background layer tokens (`--bg-deep` through `--bg-input`)
  - Border tokens (`--border-subtle`, `--border-default`, `--border-hover`, `--border-focus`)
  - Typography color tokens (`--text-primary`, `--text-secondary`, `--text-muted`, `--text-disabled`)
  - Accent tokens with soft variants (emerald, indigo, amber, cyan, rose)
  - Spacing scale tokens (`--space-xs` through `--space-2xl`)
  - Border radius tokens (`--radius-sm` through `--radius-full`)
  - Transition tokens (`--transition-fast`, `--transition-normal`, `--transition-slow`)
  - Font stack tokens (already partially exist — ensure completeness)
- [ ] Add keyframe animations: `shimmer`, `slide-in-right`, `slide-out-right`, `fade-in`, `pulse-ring`.
- [ ] Add utility classes for glass cards, buttons (primary/secondary/ghost/danger), inputs, status dots, badges.
- [ ] Add scrollbar styling using accent colors.
- [ ] Remove all existing hardcoded hex values from `index.css` and replace with token references.
- [ ] Keep `@import "tailwindcss";` — Tailwind is used for inline utilities alongside the token system.

## Dependencies
- None (this is the foundation for everything else).

## Definition of Done
- `index.css` contains the complete design token system as CSS custom properties.
- All existing utility classes (`.glass-card`, `.button-neon`, etc.) use tokens instead of hardcoded hex.
- Keyframe animations are defined and ready for component use.
