# Subtask 11_04: Frontend Visual Node Canvas & Builder UX (Verify + Wire)

## Objective
Verify the existing interactive visual workflow canvas is fully wired to the real backend run + validate endpoints and that the frontend node palette matches the backend `SUPPORTED_NODE_TYPES`. The components already exist — this is a wiring/parity pass.

## Current State (verified)
- `frontend/src/components/hubs/workflow/WorkflowCanvas.tsx` — SVG bezier edge rendering, drag connection drafting, node drag positioning, edge deletion, dot-matrix grid.
- `frontend/src/components/hubs/workflow/WorkflowNodeCard.tsx` — node cards with category icons, typed input/output handle ports (`data`, `true`, `false`, `error`, `route`), selection outline, `NODE_CONFIGS` registry, `getDefaultPortsForType()`.
- `frontend/src/components/hubs/workflow/WorkflowEditor.tsx` — property drawer with live Hub selectors (agents, collections, eval suites), save/publish, run modal trigger.
- `frontend/src/components/hubs/workflow/WorkflowRunModal.tsx` — test run modal.
- `frontend/src/services/api.ts` — `api.workflows.*` namespace (list/get/create/update/delete/run) exists.

## Tasks
1. `[x]` **Verify canvas/editor wiring** — confirm `WorkflowEditor` loads the draft graph, renders nodes/edges, saves via `PUT /{wf_id}/draft` with `If-Match` ETag, and triggers runs via `POST /{wf_id}/run`.
2. `[/]` **Node-type parity** — reconcile `NODE_CONFIGS` in `WorkflowNodeCard.tsx` with backend `GraphParser.SUPPORTED_NODE_TYPES`. Add any missing node types (e.g. `api_call`, `webhook`, `action`, `classifier`) so the palette and backend agree. Ensure `getDefaultPortsForType()` emits the same handle IDs the backend expects (`out`, `true`, `false`, `error`, `route_*`, `in`, `in_multi`).
3. `[/]` **Run modal consumes real output** — update `WorkflowRunModal.tsx` to render the real per-node SSE events (`node_start`/`node_end`/`run_end`) and the final `output_json` from the run, instead of a static placeholder.
4. `[/]` **Validate feedback** — surface `POST /{wf_id}/validate` `ValidationIssue` items (node-level errors/warnings) inline on the canvas (badge on offending node + issue list panel).
5. `[/]` **Add `api.workflows.validate()`** in `frontend/src/services/api.ts` if not present, and wire it to the editor's validate action.
