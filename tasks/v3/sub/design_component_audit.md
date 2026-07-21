# Subtask: Audit & Update All Components for Design Token Compliance

**Parent Link:** `base/02_frontend_design_system.md`

## Actionable Steps
- [x] Audit every component file in `frontend/src/components/` for hardcoded hex values in Tailwind classes.
- [x] Replace all inline hardcoded colors with design token equivalents:
  - `bg-[#15171e]` → use `var(--bg-surface)` or token-mapped Tailwind class
  - `bg-[#181a21]` → use `var(--bg-surface-alt)`
  - `bg-[#121316]` → use `var(--bg-input)`
  - `border-[#26282d]` → use `var(--border-default)`
  - `text-zinc-400` → map to `var(--text-secondary)` where appropriate
  - `bg-emerald-500/10` → use `var(--emerald-soft)`
- [x] Ensure all buttons follow the button tier system (primary/secondary/ghost/danger).
- [x] Ensure all form inputs follow the input pattern from design system.
- [x] Ensure all modals follow the modal pattern (blur overlay, proper header/footer).
- [x] Ensure font usage: headings use `--font-display`, labels are uppercase with tracking.

## Dependencies
- `sub/design_css_tokens.md` must be completed first.

## Definition of Done
- All components reference design tokens instead of hardcoded values.
- Visual appearance is consistent across all panels.
- `npm run build` passes.
