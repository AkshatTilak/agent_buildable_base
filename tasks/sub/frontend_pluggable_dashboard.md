# Subtask: Pluggable Dashboard Architecture

**Parent Link:** `base/frontend_ui.md`

## Actionable Steps
- [ ] Implement status mapping: GET `/health` → read `active_projects` array.
- [ ] Build dynamic navigation loader: disable routes/menus for inactive modules.
- [ ] Create fallback UI placeholders with enable instructions for disabled modules.

## Dependencies
- Frontend project and design system. Gateway `/health` endpoint.

## Definition of Done
- Dashboard adapts to active projects. Disabled modules show helpful fallback cards.
