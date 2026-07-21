# Subtask: React Router Setup

**Parent Link:** `base/04_frontend_routing_navigation.md`

## Actionable Steps
- [x] Install `react-router-dom` via `npm install react-router-dom`.
- [x] Update `main.tsx` to wrap `<App />` in `<BrowserRouter>`.
- [x] In `App.tsx`, replace the `activeTab` state + conditional rendering with `<Routes>`:
  - `/` → redirect to `/system`
  - `/system` → `<SystemMetrics />`
  - `/ingestion` → `<IngestionPanel />`
  - `/workflow` → `<WorkflowCanvas />`
  - `/agents` → `<AgentHub />`
  - `/evalops` → `<EvalPanel />`
  - `/settings` → Settings page (extract from config modal)
  - `*` → 404 page
- [x] Update `Sidebar.tsx` to use `<NavLink>` components instead of `<button>` with `setActiveTab`.
- [x] Remove `activeTab` state from App and Sidebar props entirely.

## Dependencies
- None (can be done independently, but Sidebar overhaul builds on top of this).

## Definition of Done
- All panels are accessible via URL routes.
- Browser back/forward navigation works.
- Direct URL access works (e.g., `localhost:5173/agents`).
- Sidebar highlights active route via `NavLink`.
