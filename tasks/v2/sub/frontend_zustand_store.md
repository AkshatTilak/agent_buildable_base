# Subtask: Frontend Zustand Store

**Parent Link:** `base/01_frontend_granular_realtime.md`

## Actionable Steps
- [x] Install `zustand` dependency in `frontend/package.json`.
- [x] Create a unified store structure in `frontend/src/store/` with sub-slices:
  - [x] `frontend/src/store/metricsSlice.ts`: Live system health, VRAM, and processing telemetry.
  - [x] `frontend/src/store/agentSlice.ts`: CRUD state for custom agents.
  - [x] `frontend/src/store/workflowSlice.ts`: Drag-and-drop workflow builder node/edge states.
- [x] Implement actions to update stores dynamically upon WebSocket message arrivals or REST API updates.
- [x] Refactor broken-down components to subscribe to specific store slices, preventing unnecessary re-renders.

## Dependencies
- `sub/frontend_component_breakdown.md`

## Definition of Done
- Zustand stores initialized and typed with TypeScript interface definitions.
- Monolithic state in `App.tsx` completely replaced by store subscriptions.
