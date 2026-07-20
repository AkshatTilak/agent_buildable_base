# Subtask: Frontend Component Breakdown

**Parent Link:** `base/01_frontend_granular_realtime.md`

## Actionable Steps
- [ ] Analyze the monolithic `frontend/src/App.tsx` (72KB) and identify candidates for extraction.
- [ ] Extract the sidebar/navigation panel to a separate component: `frontend/src/components/Sidebar.tsx`.
- [ ] Extract the telemetry / system health grid to: `frontend/src/components/SystemMetrics.tsx` (monitoring CPU, memory, VRAM, and API statuses).
- [ ] Extract the ingestion strategy and file upload logic to: `frontend/src/components/IngestionPanel.tsx`.
- [ ] Extract the LangGraph visual workflow canvas wrapper to: `frontend/src/components/WorkflowBuilder.tsx`.
- [ ] Extract the agent configuration forms and lists to: `frontend/src/components/AgentHub.tsx`.
- [ ] Extract the evaluations dashboard to: `frontend/src/components/EvalPanel.tsx`.
- [ ] Clean up `App.tsx` to serve purely as the root layout shell loading these components.

## Dependencies
- None

## Definition of Done
- `App.tsx` is reduced below 200 lines, with layout structure delegated to the new components.
- App builds successfully without syntax or TypeScript errors (`npm run build`).
