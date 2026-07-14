# Base Task: Frontend Developer Dashboard

## Objective
Build a complete React + TypeScript developer dashboard with Vite, featuring pluggable panels for SyntraFlow, GuardRoute, and EvalOps, plus a global system status panel and model registry viewer.

## Business/System Value
The frontend is the single pane of glass for operators and developers. It provides visibility into system health, model status, ingestion pipelines, and real-time interaction with the orchestration layer. Without it, all interaction is via raw API calls.

## Subtask Registry
- [ ] `sub/frontend_project_setup.md`
- [ ] `sub/frontend_design_system.md`
- [ ] `sub/frontend_pluggable_dashboard.md`
- [ ] `sub/frontend_system_status_panel.md`
- [ ] `sub/frontend_syntraflow_dashboard.md`
- [ ] `sub/frontend_guardroute_dashboard.md`
- [ ] `sub/frontend_evalops_dashboard.md`
- [ ] `sub/frontend_error_states.md`

## Complexity Rating
**High** — Full React application with real-time SSE streaming, interactive flowcharts (React Flow), data visualization, and multi-panel responsive layout.

## References
- `references/structure/frontend.md` — Full UI specifications and design tokens.
- `references/logic/guardroute.md` — Streaming chat & flowchart requirements.
- `references/logic/evalops.md` — Dashboard chart and audit log specs.
