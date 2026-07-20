# Subtask: Frontend Zustand Store

**Parent Link:** `base/01_frontend_granular_realtime.md`

## Actionable Steps
- [ ] Install `zustand` dependency in `frontend/package.json`.
- [ ] Create a unified store structure in `frontend/src/store/` with sub-slices:
  - [ ] `frontend/src/store/metricsSlice.ts`: Live system health, VRAM, and processing telemetry.
  - [ ] `frontend/src/store/agentSlice.ts`: CRUD state for custom agents.
  - [ ] `frontend/src/store/workflowSlice.ts`: Drag-and-drop workflow builder node/edge states.
- [ ] Implement actions to update stores dynamically upon WebSocket message arrivals or REST API updates.
- [ ] Refactor broken-down components to subscribe to specific store slices, preventing unnecessary re-renders.

## Dependencies
- `sub/frontend_component_breakdown.md`

## Definition of Done
- Zustand stores initialized and typed with TypeScript interface definitions.
- Monolithic state in `App.tsx` completely replaced by store subscriptions.
