# Subtask: Fix Frontend TypeScript Type Safety

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [x] Create `frontend/src/types/` directory.
- [x] Create `types/api.ts` with typed interfaces for all API responses:
  - `SystemHealthResponse` (replace `systemHealth: any` in App.tsx)
  - `ModelRegistryResponse` (replace `modelRegistry: any` in App.tsx)
  - `AgentResponse`, `AgentCreatePayload`, `AgentUpdatePayload`
  - `WorkflowResponse`, `WorkflowCreatePayload`
  - `IngestionResponse`, `JobStatusResponse`
  - `EvalRunResponse`, `TestCaseResponse`
- [x] Create `types/telemetry.ts` for WebSocket message types.
- [x] Update `App.tsx` — replace `useState<any>` with typed state.
- [x] Update `SystemMetrics.tsx` — replace `any` props with `SystemMetricsProps` interface.
- [x] Update `WorkflowCanvas.tsx` — type all `any` parameters in handlers.
- [x] Update `EvalPanel.tsx` — type all API responses.
- [x] Update `api.ts` — replace all `request<any>` calls with typed generics.
- [x] Update `IngestionPanel.tsx:9` — replace `useState<any>` for `ingestResult`.

## Dependencies
- None.

## Definition of Done
- Zero instances of `any` type in `frontend/src/` (excluding `node_modules`).
- `npm run build` passes with no TypeScript errors.
