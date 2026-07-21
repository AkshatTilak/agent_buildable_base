# Subtask: Command Palette (⌘K / Ctrl+K)

**Parent Link:** `base/04_frontend_routing_navigation.md`

## Actionable Steps
- [ ] Create `frontend/src/components/layout/CommandPalette.tsx`.
- [ ] Register global keyboard listener: `⌘K` (Mac) / `Ctrl+K` (Windows) toggles palette.
- [ ] Palette UI: centered overlay modal with search input at top, results list below.
- [ ] Navigation commands: "Go to System Metrics", "Go to Ingestion Pipeline", "Go to Workflow Builder", "Go to Agent Hub", "Go to EvalOps".
- [ ] Action commands: "Create New Agent", "Upload Document", "Run Evaluation".
- [ ] Fuzzy search: filter commands as user types (simple `includes` match is sufficient).
- [ ] Keyboard navigation: Arrow Up/Down to move selection, Enter to execute, Escape to close.
- [ ] Each result shows icon + label + keyboard shortcut hint.
- [ ] Close on backdrop click or Escape key.
- [ ] Style: `var(--bg-surface)`, `var(--border-default)`, `var(--radius-xl)`, blur overlay.

## Dependencies
- `sub/routing_react_router_setup.md` (navigation commands need router).

## Definition of Done
- ⌘K / Ctrl+K opens command palette.
- Navigation and action commands work.
- Fuzzy search filters results.
- Fully keyboard navigable.
