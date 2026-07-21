# Subtask: Fix Frontend TypeScript Type Safety

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [ ] Create `frontend/src/types/` directory.
- [ ] Create `types/api.ts` with typed interfaces for all API responses:
  - `SystemHealthResponse` (replace `systemHealth: any` in App.tsx)
  - `ModelRegistryResponse` (replace `modelRegistry: any` in App.tsx)
  - `AgentResponse`, `AgentCreatePayload`, `AgentUpdatePayload`
  - `WorkflowResponse`, `WorkflowCreatePayload`
  - `IngestionResponse`, `JobStatusResponse`
  - `EvalRunResponse`, `TestCaseResponse`
- [ ] Create `types/telemetry.ts` for WebSocket message types.
- [ ] Update `App.tsx` — replace `useState<any>` with typed state.
- [ ] Update `SystemMetrics.tsx` — replace `any` props with `SystemMetricsProps` interface.
- [ ] Update `WorkflowCanvas.tsx` — type all `any` parameters in handlers.
- [ ] Update `EvalPanel.tsx` — type all API responses.
- [ ] Update `api.ts` — replace all `request<any>` calls with typed generics.
- [ ] Update `IngestionPanel.tsx:9` — replace `useState<any>` for `ingestResult`.

## Dependencies
- None.

## Definition of Done
- Zero instances of `any` type in `frontend/src/` (excluding `node_modules`).
- `npm run build` passes with no TypeScript errors.
