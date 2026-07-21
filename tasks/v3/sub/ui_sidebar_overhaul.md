# Subtask: Sidebar Premium UI Overhaul

**Parent Link:** `base/03_frontend_premium_ui.md`

## Actionable Steps
- [x] Add collapsible sidebar toggle button — collapse to 64px icon-only mode. Expand on hover or toggle.
- [x] Persist collapsed/expanded state in `settingsSlice` (localStorage).
- [x] Add animated active indicator — sliding highlight bar that follows the active nav item (CSS transform transition).
- [x] Add "Quick Actions" section at bottom of nav (above connection status):
  - "Upload Document" shortcut
  - "Create Agent" shortcut
- [x] Add keyboard shortcut hints next to nav items (e.g., "⌘1" for System Metrics).
- [x] Update version label from "Platform V2" to "Platform V3".
- [x] Add subtle gradient divider lines between nav sections.
- [x] Use `NavLink` from react-router-dom instead of button elements (after routing is set up).

## Dependencies
- `sub/routing_react_router_setup.md` (for NavLink), `sub/fix_frontend_config_persistence.md` (for settingsSlice).

## Definition of Done
- Sidebar collapses to icon-only mode.
- Active indicator animates between nav items.
- Quick actions section visible.
- Version label shows "Platform V3".
