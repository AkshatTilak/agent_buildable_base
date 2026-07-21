# Subtask: Breadcrumbs & Top Header Bar

**Parent Link:** `base/04_frontend_routing_navigation.md`

## Actionable Steps
- [x] Create `frontend/src/components/layout/HeaderBar.tsx` — top bar spanning the content area.
- [x] Create `frontend/src/components/layout/Breadcrumbs.tsx` — dynamic breadcrumb trail.
- [x] Breadcrumbs auto-generate from current route:
  - `/system` → "Dashboard / System Metrics"
  - `/agents` → "Dashboard / Agent Hub"
  - `/ingestion` → "Dashboard / Ingestion Pipeline"
  - etc.
- [x] HeaderBar contains: breadcrumbs (left), connection status indicator (right), settings gear icon (right).
- [x] Connection status shows WebSocket status dot (emerald=connected, amber=degraded, rose=disconnected).
- [x] Style: height 56px, `var(--bg-main)` with bottom border `var(--border-subtle)`, `backdrop-filter: blur(14px)`.

## Dependencies
- `sub/routing_react_router_setup.md`.

## Definition of Done
- HeaderBar renders at top of content area.
- Breadcrumbs update dynamically based on current route.
- Connection status reflects real WebSocket state.
