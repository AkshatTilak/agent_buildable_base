# Base Task: Granular & Real-Time Frontend Refactor

## Objective
Dismantle the 1500+ line monolithic `App.tsx` and migrate the frontend to a modular architecture using Zustand for state management and WebSockets/SSE for real-time telemetry.

## Business/System Value
The current frontend is highly static and doesn't reflect the actual state of the backend processing. By breaking it into granular components and connecting it directly to backend APIs via websockets, the UI becomes a true real-time dashboard capable of tracking live ingestion pipelines, VRAM usage, and agent activities.

## Complexity Rating
High (Requires significant refactoring of React components and establishing new network protocols).

## Subtask Registry
* `[x] sub/frontend_component_breakdown.md` — Split `App.tsx` into atomic components.
* `[x] sub/frontend_zustand_store.md` — Implement Zustand stores for system health, agents, and ingestion state.
* `[x] sub/frontend_websocket_integration.md` — Establish WS/SSE connections to the backend Gateway to stream live updates.
* `[x] sub/frontend_api_integration.md` — Integrate with all existing and newly created V2 REST APIs.
