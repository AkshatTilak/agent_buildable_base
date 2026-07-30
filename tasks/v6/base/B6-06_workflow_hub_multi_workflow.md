# B6-06: Workflow Hub — Multi-Workflow Management & Versioning

> **Status:** `[x]`  
> **Owner:** `projects/guardroute`, `gateway/api/workflows.py`  
> **Secondary:** `common/models`, `common/services`  
> **Complexity:** 🔴 High (7 subtasks)

---

## Objective

Turn the singleton V5 canvas into a real **Workflow Hub**: many hubs, each holding many independently
versioned, independently runnable workflows. Add immutable `workflow_versions`, a draft/publish
lifecycle, persisted `workflow_runs` with per-node traces, duplication, import/export, and
conflict-safe concurrent editing via optimistic locking.

Node semantics, the graph parser and terminal-action constraints from V5 are **retained** — this base
task changes how workflows are stored, grouped, versioned and executed, not what a node means.

---

## Acceptance Criteria

- [x] `workflows` carries `hub_id`, `slug`, `tags_json`, `status`, `draft_version_id` and `published_version_id`; `graph_json` is moved off this table.
- [x] `workflow_versions` stores immutable numbered graph snapshots with validation results; `workflow_runs` stores execution history.
- [x] A workflow hub can hold an unbounded number of workflows; `(hub_id, slug)` is unique.
- [x] Editing mutates the draft version; publishing freezes it and opens a fresh draft on the next edit.
- [x] `PUT .../draft` honours `If-Match: <version_etag>` and returns `409` plus the server graph on a stale write — no silent overwrite.
- [x] Any prior version can be restored as a new draft; a version diff (nodes/edges added, removed, changed) is computable server-side.
- [x] Duplicate, export (JSON) and import (JSON, with reference remapping and validation) all work.
- [x] Node references to agents and collections are stored as qualified `{type, hub_id, resource_id}` objects and validated on edit, on publish, and again at execution time.
- [x] Running a workflow persists a `workflow_run` row and emits per-node rows into `eval_flow_traces` linked by `run_id`.
- [x] Run progress streams over SSE with `node_start` / `node_end` / `run_end` events; runs can be cancelled.
- [x] All routes are nested under `/hubs/{hub_id}/workflows` and guarded by `require_hub(hub_type="workflow")`; the flat `/workflows` routes are **removed**.
- [x] Existing workflows migrate into `workflow/default` as published v1, with bare agent references rewritten to qualified references against `agent/default`.
- [x] `tests/test_workflow_hub.py` covers multi-workflow CRUD, version lifecycle, etag conflict, cross-hub reference rejection, and run persistence.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-06a | Workflow, Version & Run Models + Migration | [`S6-06a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-06a.md) |
| S6-06b | Version Lifecycle Service | [`S6-06b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-06b.md) |
| S6-06c | Qualified Node References & Graph Parser Update | [`S6-06c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-06c.md) |
| S6-06d | Run Orchestration, Persistence & SSE | [`S6-06d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-06d.md) |
| S6-06e | Workflow Hub API Routes | [`S6-06e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-06e.md) |
| S6-06f | Duplicate, Import/Export & Templates | [`S6-06f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-06f.md) |
| S6-06g | Workflow Migration, Legacy Removal & Tests | [`S6-06g.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-06g.md) |

---

## References

- [`references/logic/workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) — canonical for this base task
- [`references/logic/workflow_v5.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v5.md) — node catalogue & terminal constraints (still valid)
- [`references/logic/guardroute.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/guardroute.md)
- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §5.4
