# Subtask 13_02: Draft Persistence & Non-Overlapping Node Placement

## Objective
Persist the workflow graph so node positions are remembered across reloads, push the graph to the backend draft endpoint on explicit Save, and fix node placement so newly added nodes never overlap existing ones.

## Root Cause
- `handleSaveDraft` called `api.workflows.update` → `PUT /{wf_id}`, which only updates metadata (name/description/tags) — the graph is **never saved**. The graph must go to `PUT /{wf_id}/draft`.
- `handleAddNode` used `x: 80 + (nodes.length % 3) * 280, y: 80 + Math.floor(nodes.length / 3) * 160`, which collides with existing nodes once the graph is non-empty.

## Tasks
1. `[x]` **Add draft API methods** — in `frontend/src/services/api.ts`, add `api.workflows.getDraft(hubId, wfId)` and `api.workflows.updateDraft(hubId, wfId, graph)` targeting `GET`/`PUT /{wf_id}/draft`.
2. `[x]` **Frontend-only draft persistence** — in `WorkflowEditor.tsx`, auto-persist `{ nodes, edges }` to `localStorage` under key `contained_workflow_draft_{workflowId}` on every change (skip while loading). This keeps drafts client-side per the product decision.
3. `[x]` **Save to backend draft** — change `handleSaveDraft` to call `api.workflows.updateDraft(hubId, wfId, { nodes, edges })` and clear the localStorage draft on success.
4. `[x]` **Non-overlapping placement** — rewrite `handleAddNode` to scan a grid (card ~280×170) and pick the first free slot that does not overlap any existing node position.

## Definition of Done
- Node positions persist across reloads (localStorage draft).
- Explicit Save Draft pushes the graph to `PUT /{wf_id}/draft` and clears the local draft on success.
- If the backend save fails, the localStorage draft is **not** cleared and the save-status badge returns to "Unsaved Changes".
- Newly added nodes never overlap existing nodes.

## Test Cases

### Backend (`tests/test_workflow_canvas_fixes.py`)
| # | Test | Expected |
|---|------|----------|
| B1 | `test_draft_put_returns_workflow_version` | `PUT /{wf_id}/draft` with valid graph → HTTP 200; `nodes` and `edges` in response match input |
| B2 | `test_draft_put_with_empty_graph` | `PUT /{wf_id}/draft` with `{ nodes: [], edges: [] }` → HTTP 200; not 422 |
| B3 | `test_metadata_update_does_not_clobber_draft` | Save draft → update name via `PUT /{wf_id}` → `GET /{wf_id}/draft` still returns original graph |

### Frontend Manual
| # | Scenario | Expected |
|---|----------|----------|
| F1 | Draft persists across hard reload | Move node → reload tab → node position unchanged |
| F2 | Explicit save clears localStorage | Click "Save Draft" (success) → DevTools → Application → localStorage key `contained_workflow_draft_{id}` is absent |
| F3 | Save failure preserves localStorage | Simulate 500 on `PUT /{wf_id}/draft` → save-status badge shows "Unsaved Changes"; localStorage draft still present |
| F4 | Non-overlapping: 9 sequential adds | Add 9 nodes via palette → none overlap visually |
| F5 | Non-overlapping: add to full row | Fill first row (3 nodes at x=80,360,640) → add 4th → placed at x=80, y=250 (next row) |
