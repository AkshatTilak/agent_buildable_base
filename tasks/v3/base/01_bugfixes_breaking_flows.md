# Base Task: Bugfixes & Breaking Flow Resolution

## Objective
Audit and fix all identified v2 breaking flows, code quality gaps, hardcoded values, type safety issues, and missing error handling across the entire stack.

## Business/System Value
V2 shipped features fast but left multiple correctness gaps: hardcoded telemetry values, missing TypeScript types, no loading/error states, config modal that doesn't persist, browser `confirm()` for destructive actions, and a frontend that silently fails when the backend is unreachable. These must be resolved before any new features are built.

## Complexity Rating
High (Touches every layer — frontend, gateway, common library, and infrastructure).

## Identified Issues

### Frontend Breaking Flows
1. **Config modal doesn't save:** `App.tsx` settings modal uses `defaultValue` inputs — values are never captured or persisted. Gateway URL and API key are discarded on close.
2. **Browser `confirm()` for deletion:** `AgentHub.tsx:100` uses native `confirm()` which is ugly, non-themeable, and blocks the JS thread. Must be a custom confirm modal.
3. **All `any` types in components:** `SystemMetrics`, `App`, `WorkflowCanvas`, `EvalPanel` all use `any` for props and state — zero type safety.
4. **No loading states:** All panels render immediately with default/zero values. No skeleton loaders, no loading spinners when fetching from API.
5. **No error states:** If backend is unreachable, panels show stale zeros with no indication of failure. No error banners, no retry buttons.
6. **No toast notifications:** CRUD operations (create/edit/delete agent, save workflow, run eval) show inline status text that auto-clears — no persistent, accessible notifications.
7. **Telemetry hardcoded port:** `telemetry.ts:13` hardcodes port `8000` — should read from settings.
8. **API client hardcodes API key:** `api.ts:12` hardcodes `"sk_live_default_key"` — should be configurable.
9. **Stale App.css:** `App.css` contains Vite starter boilerplate CSS (`.hero`, `.counter`, `#center`) that is completely unused.
10. **index.html missing SEO:** Title is "frontend", no meta description, no proper favicon reference.

### Backend Breaking Flows
11. **Telemetry hardcoded values:** `telemetry.py:41-43` hardcodes `vram_usage_mb: 4096`, `vram_total_mb: 16384`, `active_agents: 2`. Must query actual state.
12. **Health endpoint no latency:** `health.py` checks service connectivity but doesn't report response latency — frontend can't show degraded performance.
13. **No documents listing API:** Frontend has no way to browse previously ingested documents. Missing `GET /api/syntraflow/documents` endpoint.
14. **No job listing API:** Frontend can't show historical ingestion jobs. Missing `GET /api/syntraflow/jobs` with filtering.
15. **VRAM budget unrealistic:** `settings.py:86` defaults `VRAM_BUDGET_MB=20000` — unrealistic for a 16GB RAM system.

## Subtask Registry
* `[x] sub/fix_frontend_config_persistence.md` — Fix config modal to persist gateway URL and API key.
* `[x] sub/fix_frontend_type_safety.md` — Replace all `any` types with proper TypeScript interfaces.
* `[ ] sub/fix_frontend_loading_error_states.md` — Add loading skeletons and error banners to all panels.
* `[ ] sub/fix_frontend_confirm_modal.md` — Replace browser `confirm()` with themed confirm modal.
* `[ ] sub/fix_frontend_stale_assets.md` — Clean up App.css boilerplate, fix index.html SEO.
* `[x] sub/fix_backend_telemetry_hardcodes.md` — Replace hardcoded telemetry values with actual metrics.
* `[x] sub/fix_backend_documents_jobs_api.md` — Add documents listing and jobs listing endpoints.
* `[x] sub/fix_backend_settings_defaults.md` — Fix VRAM budget and add platform version to settings.
