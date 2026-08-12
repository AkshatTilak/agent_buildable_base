# Workflow Hub — Multi-Workflow Management (V6)

> **Status:** Authoritative for V6+
> **Supersedes:** [`workflow_v5.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v5.md)
> (node semantics, graph parser, and terminal constraints from V5 remain valid and are **not** restated here)
> **Owner modules:** `projects/guardroute`, `gateway/api/workflows.py`, `frontend/src/components/workflow`

V5 delivered a capable single-graph builder: the platform effectively held one editable workflow at a
time, persisted in the `workflows` table but surfaced through a singleton `currentWorkflow` store slice.
V6 turns the builder into a proper **Workflow Hub**: many hubs, each holding many independently
versioned, independently runnable workflows.

---

## 1. What V6 Adds

| Capability | V5 | V6 |
|---|---|---|
| Number of editable workflows | effectively 1 | unbounded, per hub |
| Grouping | none | Workflow Hubs (tenancy) + optional folder tags |
| Versioning | none — saves overwrite | immutable `workflow_versions` with draft/published pointers |
| Run history | ephemeral | persisted `workflow_runs` with per-node traces |
| Duplication / templates | none | duplicate, export/import JSON, seed templates |
| Agent sourcing | any agent globally | agents from this hub's linked agent hubs only |
| Concurrency safety | last-write-wins | optimistic locking via `version_etag` |

Node types, the graph parser, state schema, and terminal-action constraints are unchanged from V5.
See [`workflow_v5.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v5.md)
§ Node Catalogue and § Terminal Constraints.

---

## 2. Data Model

### 2.1 `workflows` (modified)

```text
workflows
  id                    String(36) PK
  hub_id                String(36) FK hubs.id ON DELETE CASCADE, NOT NULL, indexed
  name                  String(120) not null
  slug                  String(120) not null
  description           Text nullable
  tags_json             JSON default []            # freeform grouping labels within a hub
  status                String(20) default "draft" # draft | published | archived
  published_version_id  String(36) FK workflow_versions.id nullable
  draft_version_id      String(36) FK workflow_versions.id nullable
  created_by            String(36) FK users.id
  created_at / updated_at

  UNIQUE (hub_id, slug)
```

`graph_json` moves **off** `workflows` and onto `workflow_versions`.

### 2.2 `workflow_versions` (new)

```text
workflow_versions
  id              String(36) PK
  workflow_id     String(36) FK workflows.id ON DELETE CASCADE, indexed
  version_number  Integer not null            # monotonic per workflow, starts at 1
  graph_json      JSON not null               # nodes + edges + viewport
  change_note     String(255) nullable
  is_valid        Boolean default false       # result of the last graph validation pass
  validation_json JSON nullable               # errors/warnings from the parser
  created_by      String(36) FK users.id
  created_at      DateTime

  UNIQUE (workflow_id, version_number)
```

Versions are **immutable** once created. Editing the canvas mutates the *draft* version in place until
it is published; publishing freezes it and opens a new draft on the next edit.

### 2.3 `workflow_runs` (new)

```text
workflow_runs
  id            String(36) PK
  hub_id        String(36) FK hubs.id NOT NULL, indexed
  workflow_id   String(36) FK workflows.id ON DELETE CASCADE, indexed
  version_id    String(36) FK workflow_versions.id
  trigger       String(20)   # manual | api | eval | schedule
  input_json    JSON
  output_json   JSON nullable
  status        String(20)   # queued | running | succeeded | failed | cancelled
  error_message Text nullable
  node_count    Integer nullable
  duration_ms   Integer nullable
  started_by    String(36) FK users.id nullable
  started_at / finished_at
```

Per-node execution detail is written to the existing `eval_flow_traces` table (now carrying `hub_id` and
`run_id` → `workflow_runs.id`), so the Eval Hub can replay and assert on any intermediate state without a
second tracing mechanism.

---

## 3. API Surface

All routes are nested under a workflow hub and guarded by `require_hub(hub_type="workflow", ...)`.

| Method | Path | Min role |
|---|---|---|
| `GET` | `/hubs/{hub_id}/workflows` | viewer |
| `POST` | `/hubs/{hub_id}/workflows` | contributor |
| `GET` | `/hubs/{hub_id}/workflows/{wf_id}` | viewer |
| `PATCH` | `/hubs/{hub_id}/workflows/{wf_id}` | contributor |
| `DELETE` | `/hubs/{hub_id}/workflows/{wf_id}` | maintainer |
| `POST` | `/hubs/{hub_id}/workflows/{wf_id}/duplicate` | contributor |
| `GET` | `/hubs/{hub_id}/workflows/{wf_id}/versions` | viewer |
| `PUT` | `/hubs/{hub_id}/workflows/{wf_id}/draft` | contributor |
| `POST` | `/hubs/{hub_id}/workflows/{wf_id}/publish` | contributor |
| `POST` | `/hubs/{hub_id}/workflows/{wf_id}/versions/{v}/restore` | contributor |
| `POST` | `/hubs/{hub_id}/workflows/{wf_id}/validate` | viewer |
| `POST` | `/hubs/{hub_id}/workflows/{wf_id}/run` | contributor |
| `GET` | `/hubs/{hub_id}/workflows/{wf_id}/runs` | viewer |
| `GET` | `/hubs/{hub_id}/workflows/{wf_id}/runs/{run_id}` | viewer |
| `GET` | `/hubs/{hub_id}/workflows/{wf_id}/export` | viewer |
| `POST` | `/hubs/{hub_id}/workflows/import` | contributor |

`PUT .../draft` carries an `If-Match: <version_etag>` header. A stale etag returns `409` with the
server's current graph so the UI can surface a non-destructive conflict dialog.

`POST .../run` streams progress over SSE (`event: node_start | node_end | run_end`) reusing the V5
streaming conventions.

---

## 4. Cross-Hub Resource Binding

A workflow node that references an agent or a collection stores a **qualified reference**, never a bare id:

```json
{ "type": "agent", "hub_id": "hub_9f2…", "agent_id": "agt_31c…" }
{ "type": "collection", "hub_id": "hub_4b8…", "collection_id": "col_77a…" }
```

Every such reference is validated through `common/services/hub_resolver.py` at three points:

1. **On canvas edit** — the node property panel only offers resources from hubs the workflow hub is
   linked to, so invalid references cannot normally be authored.
2. **On publish** — validation fails with a precise per-node error if a link has since been revoked.
3. **At execution time** — re-checked before the node runs; a revoked link fails the run with
   `HUB_LINK_REVOKED` rather than silently reading across a tenancy boundary.

The **Agent Picker** in the property drawer groups options by source agent hub and shows a badge when a
hub link is missing, with an inline "Request link" action for maintainers.

### Frontend resource sourcing (V7 fix)

The editor's property drawer dropdowns (agent, retrieval/collection, eval suite, DB credential) must
source resources from **linked** hubs, not the current workflow hub. `WorkflowEditor.tsx` implements
`fetchLinkedHubResources()`:

1. Call `GET /hubs/{hub_id}/links` to list outgoing links.
2. Build a hub-id set = `{ currentHubId } ∪ { link.target_hub_id }`.
3. For each hub id, fetch agents (`GET /hubs/{hid}/agents`), collections
   (`GET /hubs/{hid}/ingestion/collections` → `res.collections`), eval suites, and DB credentials,
   merging results. Each call is try/catch-wrapped so a wrong-hub-type call is silently skipped.

This is required because `GET /hubs/{hub_id}/agents` and `GET /hubs/{hub_id}/ingestion/collections`
enforce `hub_type="agent"` / `hub_type="ingestion"` respectively and will reject a workflow hub id.

---

## 5. Frontend

| Route | Component | Purpose |
|---|---|---|
| `/hubs/workflow/:hubId` | `WorkflowHubOverview.tsx` | Hub landing: counts, recent runs, health |
| `/hubs/workflow/:hubId/workflows` | `WorkflowList.tsx` | Grid/table, search, tag filter, status chips, duplicate/export row actions |
| `/hubs/workflow/:hubId/workflows/:wfId` | `WorkflowEditor.tsx` | ReactFlow canvas + property drawer + version rail |
| `/hubs/workflow/:hubId/workflows/:wfId/runs` | `WorkflowRuns.tsx` | Run history table with per-node trace drill-down |
| `/hubs/workflow/:hubId/links` | `HubLinksPanel.tsx` | Shared component; manage agent/ingestion links |

### Store changes

`frontend/src/store/workflowSlice.ts` is rewritten. The singleton `currentWorkflow`, `nodes`, and
`edges` fields are **removed** in favour of:

```ts
workflowsByHub: Record<string, WorkflowSummary[]>
editorState: {
  workflowId: string | null
  graph: WorkflowGraph
  versionEtag: string | null
  dirty: boolean
  validation: ValidationResult | null
}
runsByWorkflow: Record<string, WorkflowRun[]>
```

Canvas edits debounce-autosave the draft (2s idle) and show an explicit `Saved • v7 draft` /
`Unsaved changes` / `Conflict` state in the editor header.

### Draft persistence & graph loading (V7 fix)

* **Graph source on load** — `WorkflowEditor.tsx` resolves the graph in priority order:
  localStorage draft → navigation `starterGraph` (from the Create dialog template) → backend
  `draft_graph` (from `GET /{wf_id}`, field `draft_graph`, **not** `graph_json`) → empty graph.
* **Frontend-only drafts** — the graph is auto-persisted to `localStorage` under
  `contained_workflow_draft_{workflowId}` on every change. Only an explicit **Save Draft** pushes the
  graph to the backend via `PUT /{wf_id}/draft` (which returns `WorkflowVersionDetail`), then clears the
  local draft. `PUT /{wf_id}` remains metadata-only (name/description/tags) and must **not** be used to
  save the graph.
* **Starter templates** — `CreateWorkflowDialog.tsx` builds starter `{ nodes, edges }` for the
  `rag` / `classifier` / `multi-agent` templates and passes them to the editor via navigation state, so
  the canvas is seeded instead of empty.
* **Non-overlapping placement** — `handleAddNode` scans a grid (~280×170 per card) and picks the first
  free slot that does not overlap any existing node position.

### Canvas interactivity & dropdown loading (V7 fix)

* **Pan / zoom** — `WorkflowCanvas.tsx` maintains a `{ x, y, zoom }` viewport transform applied to the
  node layer; edges are recomputed against the transformed viewport so they stay aligned. Pan by
  dragging the empty background; zoom via mouse wheel (cursor-centered) and `+`/`-` buttons, clamped
  ~0.25×–2.5×.
* **Keyboard shortcuts** — scoped to the editor: `Del`/`Backspace` delete node, `Cmd/Ctrl+D` duplicate,
  `Cmd/Ctrl+S` save draft, `Cmd/Ctrl+Z` / `Cmd/Ctrl+Shift+Z` undo/redo (bounded history), `+`/`-` zoom,
  `0` fit view. Suppressed while typing in inputs/selects/textareas.
* **Fullscreen** — a toolbar button expands the canvas to the viewport (Fullscreen API or fixed
  overlay) and restores on exit; pan/zoom state is preserved.
* **Dropdown loading states** — the property drawer tracks per-resource loading/error for agents,
  collections, eval suites, and DB credentials, rendering a spinner while fetching and a clear
  empty/error state on failure.

### UX requirements

* Workflow list supports keyboard navigation, multi-select, and bulk archive.
* The editor has an always-visible validation strip; clicking an error focuses and highlights the
  offending node.
* Version rail shows an inline diff (node/edge added, removed, changed) between any two versions.
* Unsaved-changes guard on navigation away from the editor.
* Canvas actions have keyboard shortcuts (`Cmd+S` publish, `Cmd+D` duplicate node, `Del` remove,
  `Cmd+Z`/`Cmd+Shift+Z` undo/redo backed by a bounded history stack).

---

## 6. Migration Notes

* Every existing `workflows` row is moved into the `workflow/default` seed hub.
* Its `graph_json` becomes `workflow_versions` v1, referenced as both `draft_version_id` and
  `published_version_id`; `status` is set to `published`.
* `graph_json` is then dropped from `workflows`.
* Bare `agent_id` node references in legacy graphs are rewritten to qualified references pointing at the
  `agent/default` seed hub, and the corresponding `workflow → agent` hub link is seeded.
