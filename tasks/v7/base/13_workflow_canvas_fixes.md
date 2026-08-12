# Base Task 13: Workflow Canvas UX Fixes — Defaults, Draft Persistence, Cross-Hub Links & Links Panel (v7)

## Objective
Fix four concrete frontend defects in the Interactive Visual Workflow Builder that block real usage of the canvas:

1. **Starter defaults never appear on the canvas** — the Create Workflow dialog's template selector (blank / RAG / classifier / multi-agent) is a no-op; the editor always opens an empty canvas.
2. **Node positions are not remembered and new nodes overlap** — the graph is never persisted (the editor saves to the metadata-only `PUT /{wf_id}` endpoint instead of the graph `PUT /{wf_id}/draft` endpoint), and `handleAddNode` places nodes on a naive grid that collides with existing nodes.
3. **Cross-hub links fail in the editor** — the agent / retrieval (ingestion/vector) property dropdowns fetch from the *current workflow hub* via `api.agents.list(hubId)` / `api.ingestion.collections.list(hubId)`, but those endpoints require an **agent** / **ingestion** hub respectively, so they always fail. Resources must be fetched from **linked** hubs.
4. **Links page returns 404 "Not Found"** — `HubLinksPanel` calls `GET /api/hubs/{hubId}/dependents`, which does not exist; the backend exposes incoming links via `GET /{hub_id}/links?direction=incoming`.

> **Scope note:** All fixes are **frontend-only**. Drafts are persisted client-side (localStorage per workflow) and only pushed to the backend draft endpoint on explicit Save. No backend schema or API changes are required.

## Current State (verified against codebase)
- **`frontend/src/components/hubs/workflow/CreateWorkflowDialog.tsx`** — has a `template` state (blank/rag/classifier/multi-agent) but `handleSubmit` only sends `name`/`description`; the template is never used.
- **`frontend/src/components/hubs/workflow/WorkflowEditor.tsx`** — reads `(data as any).graph_json` on load, but `GET /{wf_id}` returns `WorkflowDetail` with `draft_graph` (a `WorkflowGraph` object), not `graph_json` → graph never loads. `handleSaveDraft` calls `api.workflows.update` → `PUT /{wf_id}` (metadata only, no graph). `handleAddNode` uses `x: 80 + (nodes.length % 3) * 280` which overlaps existing nodes.
- **`frontend/src/components/hubs/workflow/WorkflowLibrary.tsx`** — `onSuccess` navigates to the editor without passing any starter graph.
- **`frontend/src/services/api.ts`** — `api.hubs.links.dependents` hits `/api/hubs/{hubId}/dependents` (404). No `api.workflows.updateDraft` / `getDraft` methods exist.
- **`frontend/src/components/hubs/HubLinksPanel.tsx`** — calls `api.hubs.links.dependents(hub.id)` for incoming links.
- **Backend** (`gateway/api/hubs.py`) — exposes `GET /{hub_id}/links?direction=incoming|outgoing`; there is **no** `/dependents` endpoint.
- **Backend** (`gateway/api/workflows.py`) — graph is saved via `PUT /{wf_id}/draft` (returns `WorkflowVersionDetail`), **not** `PUT /{wf_id}` (metadata only).
- **Backend** (`common/models/hub_enums.py`) — `ALLOWED_LINK_DIRECTIONS` includes `(workflow → agent)` and `(workflow → ingestion)`.

## Subtasks
1. `[x]` `sub_13_01_starter_template_defaults.md`: Make the Create Workflow dialog's template selector seed the canvas with starter nodes/edges (RAG, classifier, multi-agent), and make the editor load the graph from the correct source (`draft_graph` / starter graph / localStorage).
2. `[x]` `sub_13_02_draft_persistence_and_node_placement.md`: Persist the graph as a frontend-only draft (localStorage per workflow), push to the backend `PUT /{wf_id}/draft` on explicit Save, and fix `handleAddNode` to place nodes in a free grid slot without overlap.
3. `[x]` `sub_13_03_cross_hub_resource_fetching.md`: Fetch agents / collections / eval suites / credentials from **linked** hubs (via `api.hubs.links.list`) plus the current hub, so the property drawer dropdowns populate correctly.
4. `[x]` `sub_13_04_links_panel_dependents_fix.md`: Fix `api.hubs.links.dependents` to call `GET /{hub_id}/links?direction=incoming` so the Links panel loads without a 404.

## Definition of Done
- Creating a workflow with a non-blank template opens the editor with the starter nodes/edges already on the canvas.
- Node positions persist across reloads (localStorage draft) and are pushed to the backend draft on explicit Save.
- Newly added nodes never overlap existing nodes.
- Agent / retrieval property dropdowns list resources from linked agent / ingestion hubs.
- The Links panel (`/hubs/workflow/{hubId}/links`) loads without a 404 and shows outgoing + incoming links.

## Test Cases
> Backend tests live in `tests/test_workflow_canvas_fixes.py` (pytest + in-memory SQLite, same fixture pattern as `test_workflow_routes.py`).

### Backend API Tests (pytest)

| # | Test Name | Scenario | Expected |
|---|-----------|----------|----------|
| B1 | `test_draft_put_returns_workflow_version` | `PUT /{wf_id}/draft` with a valid `{ nodes, edges }` JSON body | HTTP 200; response body contains `nodes` and `edges` matching input |
| B2 | `test_draft_get_returns_saved_graph` | After B1, `GET /{wf_id}/draft` | HTTP 200; returned `nodes`/`edges` match what was saved in B1 |
| B3 | `test_draft_put_with_empty_graph` | `PUT /{wf_id}/draft` with `{ nodes: [], edges: [] }` | HTTP 200; draft saved as empty graph (not 422) |
| B4 | `test_links_direction_incoming` | `GET /{hub_id}/links?direction=incoming` for a hub that is the *target* of two links | HTTP 200; returns array of exactly 2 `HubLink` objects with `target_hub_id == hub_id` |
| B5 | `test_links_direction_outgoing_default` | `GET /{hub_id}/links` (no `direction` param) | HTTP 200; returns only outgoing links for that hub (not incoming) |
| B6 | `test_links_direction_incoming_empty` | `GET /{hub_id}/links?direction=incoming` for a hub with no incoming links | HTTP 200; returns `[]` (not 404) |
| B7 | `test_metadata_update_does_not_clobber_draft` | Save draft via `PUT /{wf_id}/draft` then update name via `PUT /{wf_id}` | `GET /{wf_id}/draft` still returns the original graph; `GET /{wf_id}` returns updated name |

### Frontend / Manual Browser Tests

| # | Scenario | Steps | Expected |
|---|----------|-------|----------|
| F1 | RAG template seeds canvas | Create workflow → select "RAG Q&A Pipeline" → submit | Editor opens with Start, Retrieval, Agent, FinalMessage nodes already on canvas, connected in sequence |
| F2 | Classifier template seeds canvas | Select "Classifier → Agent Router" → submit | Router node with 3 route outputs + Agent + FinalMessage appear; all edges connected |
| F3 | Multi-agent template seeds canvas | Select "Multi-Agent Fan-Out" → submit | Start → 2 Agent nodes → Gather → FinalMessage; two separate edges from Start to agents |
| F4 | Blank template = empty canvas | Select "Blank Canvas" → submit | Editor opens empty; zero nodes/edges visible |
| F5 | Draft persists across reload | Open editor → move a node → hard-reload tab | Node position unchanged; localStorage draft loaded without re-fetching from backend |
| F6 | Explicit Save clears localStorage draft | Open editor with a local draft → click "Save Draft" → check DevTools → Application → Local Storage | `contained_workflow_draft_{wfId}` key is removed after successful `PUT /{wf_id}/draft` |
| F7 | Non-overlapping node placement (sequential) | Add 9 nodes sequentially via the Add Node palette | No two nodes share the same grid slot; all are visible and individually selectable |
| F8 | Add node to starter graph | Open a RAG template workflow → add a new "agent" node | New node appears in a free grid slot; existing 4 nodes are undisturbed |
| F9 | Cross-hub agents populate dropdown | Workflow hub linked to an agent hub with 3 agents → open agent node property drawer | All 3 agents from the linked agent hub appear in the dropdown (not empty) |
| F10 | Cross-hub collections populate dropdown | Workflow hub linked to an ingestion hub with 2 collections | Both collections appear in the retrieval node property drawer |
| F11 | No-link hub silently shows empty dropdown | Workflow hub has no linked hubs → open agent node property drawer | Dropdown shows a clear "No linked agents available" state; no console error or crash |
| F12 | Linked-hub resources deduped | Same agent hub linked twice (edge case) → open property drawer | Each agent appears only once in the dropdown (no duplicates) |
| F13 | Links panel loads without 404 | Navigate to `/hubs/workflow/{hubId}/links` | Panel renders outgoing and incoming tables; no 404 error in Network DevTools tab |
| F14 | localStorage draft scoped per workflow | Open workflow A → make changes → switch to workflow B | Workflow B loads its own draft (or clean backend state); no data bleed from workflow A's localStorage key |
