# Subtask 13_01: Starter Template Defaults on the Canvas

## Objective
Make the Create Workflow dialog's template selector actually seed the canvas with starter nodes/edges, and make the editor load the graph from the correct source so saved graphs (and starter graphs) appear instead of an always-empty canvas.

## Root Cause
- `CreateWorkflowDialog.tsx` had a `template` state but `handleSubmit` only sent `name`/`description`; the template was never used.
- `WorkflowEditor.tsx` read `(data as any).graph_json` on load, but `GET /{wf_id}` returns `WorkflowDetail` with `draft_graph` (a `WorkflowGraph` object with `nodes`/`edges`), not `graph_json`. So the graph never loaded → always-empty canvas.

## Tasks
1. `[x]` **Build starter graphs** — in `CreateWorkflowDialog.tsx`, add a `buildStarterGraph(template)` helper that emits `{ nodes, edges }` for `rag`, `classifier`, and `multi-agent` templates. Node shapes must match `WorkflowNodeData` (id, type, label, data, position, ports) and use the same handle IDs the backend expects (`out`, `in`, `true`, `false`, `route_*`, `in_multi`, `in_multi_2`).
2. `[x]` **Pass the starter graph to the editor** — change `onSuccess` to `(workflowId, starterGraph?)` and have `WorkflowLibrary.tsx` navigate to the editor with `state: { starterGraph }`.
3. `[x]` **Load the graph from the correct source** — in `WorkflowEditor.tsx`, resolve the graph in priority order: localStorage draft → navigation `starterGraph` → backend `draft_graph` → empty graph. Read `(data as any).draft_graph` (not `graph_json`).

## Definition of Done
- Creating a workflow with a non-blank template opens the editor with the starter nodes/edges already on the canvas.
- Opening an existing workflow with a saved draft graph renders its nodes/edges.
- If localStorage contains a valid draft, it takes priority over the starter graph and the backend draft.
- If localStorage data is corrupt/unparseable, it is silently discarded and the next source in the priority chain is used.

## Test Cases

### Backend (via integration — `tests/test_workflow_canvas_fixes.py`)
| # | Test | Expected |
|---|------|----------|
| B1 | `test_draft_get_returns_draft_graph_after_put` | Save a graph with `nodes=[{id:"n1",...}]` via `PUT /{wf_id}/draft`; call `GET /{wf_id}`; field `draft_graph.nodes[0].id == "n1"` |
| B2 | `test_blank_workflow_has_empty_draft_graph` | Newly created workflow `GET /{wf_id}` returns `draft_graph` with empty `nodes: []` |

### Frontend Manual
| # | Scenario | Expected |
|---|----------|----------|
| F1 | RAG template → canvas has exactly 4 nodes and 3 edges | Verified by counting nodes/edges in the React DevTools or visually |
| F2 | Priority order: localStorage > starterGraph | Pre-seed localStorage with a 1-node graph → create RAG workflow in same workflow id context → editor shows localStorage nodes, not starter |
| F3 | Corrupt localStorage silently discarded | Manually set `contained_workflow_draft_{id}` to `"not json"` → open editor → editor falls through to backend `draft_graph` |

