# B6-11: Legacy Decommission, Data Migration & Verification

> **Status:** `[x]`  
> **Owner:** `gateway`, `projects`, `frontend`, `tests`  
> **Secondary:** `migrations`, `agent_buildable_base/references`  
> **Complexity:** 🔴 High (6 subtasks)

---

## Objective

V6 is a **hard cutover**. This base task exists so that removal is treated as first-class work with its
own acceptance criteria and verification, rather than being left as incidental cleanup. Every route,
module, component, column, type and store field that the hub architecture replaces must be deleted, and
the deletion must be *proven* by grep-based and behavioural checks.

This base task runs **last** — it may only begin once `B6-01` … `B6-10` are complete.

---

## Acceptance Criteria

### Backend removal

- [x] Flat routes are gone: `GET/POST /agents`, `/agents/{id}`, `/agents/batch-invoke`, `/agents/route`, `/workflows`, `/workflows/{id}`, `/api/syntraflow/collections`, `/api/syntraflow/search`, `/api/syntraflow/ingest`, `/api/syntraflow/jobs`, and the `/api/evalops` suite/run endpoints.
- [x] `gateway/auth/dependencies.py::require_role` is narrowed to platform roles only; no hub-scoped route references it.
- [x] `users.role`, `users.is_active`, `users.provider`, `users.provider_id` and `syntraflow_collections.tenant_id` no longer appear in any model, schema, query or migration head.
- [x] `workflows.graph_json` is removed from the model and all code paths.
- [x] Every string literal `"editor"` and `"viewer"` used as a *platform* role is removed (hub role `viewer` remains and is unambiguous by context).
- [x] The identifier and user-facing label "MCP Hub" is renamed to "MCP Registry" across routes, components, copy and references.

### Frontend removal

- [x] Legacy top-level views are deleted: the old `AgentHub.tsx`, `WorkflowCanvas.tsx` singleton entry, `IngestionPanel.tsx`, `EvalPanel.tsx` and `CollectionManager.tsx` at their V5 paths, replaced by their hub-workspace equivalents.
- [x] `workflowSlice.currentWorkflow`, `.nodes` and `.edges` singleton fields no longer exist.
- [x] Flat API helpers in `services/api.ts` that do not take a `hubId` are removed.
- [x] Legacy routes `/ingestion`, `/workflow`, `/agents`, `/evalops` are removed — not aliased, not redirected.
- [x] `types/api.ts` contains no interface referencing a removed field; `npx tsc --noEmit` is clean.

### Migration & verification

- [x] A dry-run harness restores a representative V5 dump, runs the full V6 migration chain, and asserts row counts, seeded hubs, seeded links, backfilled `hub_id` values and rebuilt constraints.
- [x] `alembic downgrade` back to the V5 head succeeds and the V5 test suite passes against the downgraded schema.
- [x] Re-running `alembic upgrade head` on an already-migrated database is a clean no-op.
- [x] A grep-based verification script (`scripts/verify_v6_cutover.py`) fails with a non-zero exit if any removed symbol, route or column string is found outside `tasks/` and `references/`.
- [x] Every reference document under `references/` is re-read and confirmed free of stale V5 claims.
- [x] A full-system verification matrix is executed and recorded: fresh install, migrated install, each hub type end-to-end, invite + approval flows, cross-hub denial, and the frontend build.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-11a | Backend Legacy Removal Sweep | [`S6-11a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-11a.md) |
| S6-11b | Frontend Legacy Removal Sweep | [`S6-11b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-11b.md) |
| S6-11c | Schema & Type Surface Cleanup | [`S6-11c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-11c.md) |
| S6-11d | Migration Dry-Run & Rollback Harness | [`S6-11d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-11d.md) |
| S6-11e | Cutover Verification Script | [`S6-11e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-11e.md) |
| S6-11f | Full-System Verification Matrix | [`S6-11f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-11f.md) |

---

## References

- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §6, §9 — the authoritative removal list
- [`prompts/work_verification.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/prompts/work_verification.md)
- [`references/deployment/deployment_guide.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/deployment/deployment_guide.md)
