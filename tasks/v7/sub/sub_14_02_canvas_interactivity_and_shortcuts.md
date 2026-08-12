# Subtask 14_02: Canvas Interactivity — Pan, Zoom & Keyboard Shortcuts

## Objective
Make the workflow canvas fully interactive: support panning, zooming, and smooth node movement, and add keyboard shortcuts for common actions.

## Root Cause
- `WorkflowCanvas.tsx` supports node dragging and connection drafting but has **no** pan, zoom, or keyboard shortcuts. The canvas is a fixed-size `relative` div with a dot-matrix background and no viewport transform.

## Tasks
1. `[x]` **Add a viewport transform** — introduce a `{ x, y, zoom }` viewport state in `WorkflowCanvas.tsx` (or lift it to `WorkflowEditor.tsx`). Apply it as a CSS `transform: translate(${vx}px, ${vy}px) scale(${zoom})` on the node layer. Recompute `getPortCoordinates()` to return positions in **screen space** (applying the inverse of the viewport transform) so that SVG edges stay aligned with the transformed nodes.
2. `[x]` **Pan** — support panning by Space+drag on the canvas background. Middle-mouse drag and two-finger trackpad swipe are stretch goals. Do not activate pan when the user drags a node (distinguish by checking `draggingNodeId`). Clamp pan within ±4000px to prevent infinite drift.
3. `[x]` **Zoom** — support zoom via mouse wheel (centred on cursor: adjust both `zoom` and `{x,y}` so the point under the cursor stays fixed). Add +/- toolbar buttons. Clamp zoom between 0.25× and 2.5×.
4. `[x]` **Node positions in graph coordinates** — node `position` must always be stored in **graph coordinates** (before viewport transform), not screen coordinates. `handleMouseMove` in the canvas must convert screen deltas back to graph coordinates using the current `zoom` factor.
5. `[x]` **Keyboard shortcuts** — register shortcuts scoped to the editor container (not window, to avoid interfering with other page inputs): `Delete`/`Backspace` → delete selected node+edges; `Cmd/Ctrl+D` → duplicate selected node (new id, offset 30px); `Cmd/Ctrl+S` → trigger `handleSaveDraft`; `Cmd/Ctrl+Z` → undo (pop history stack); `Cmd/Ctrl+Shift+Z` → redo (push forward stack); `+`/`-` → zoom step ±0.1; `0` → fit-to-view (reset viewport to show all nodes). Guard all shortcuts with `if (event.target instanceof HTMLInputElement || event.target instanceof HTMLTextAreaElement || event.target instanceof HTMLSelectElement) return;`.
6. `[x]` **Undo/redo history** — maintain a `history` stack and `historyIndex` in `WorkflowEditor.tsx`. Push a snapshot `{ nodes, edges }` on every user-driven change (add/delete/move node, add/delete edge). Cap the stack at **50 entries** to bound memory. Undo = decrement index and restore snapshot; Redo = increment index.

## Definition of Done
- The canvas pans (Space+drag), zooms (wheel + buttons), and keeps nodes/edges aligned under the viewport transform.
- Node drag positions persist correctly in graph coordinates (independent of zoom).
- Keyboard shortcuts work for delete, duplicate, save, undo/redo, and zoom, and are suppressed while typing in form fields.
- Undo/redo history works up to 50 steps; older steps are discarded.

## Test Cases

### Frontend Manual
| # | Scenario | Steps | Expected |
|---|----------|-------|----------|
| F1 | Pan moves the canvas | Hold Space → drag canvas right | All nodes shift right; dot-matrix background scrolls; edges remain attached to nodes |
| F2 | Pan does not move nodes | Hold Space → drag a node | Node does not move (pan mode takes precedence) |
| F3 | Zoom wheel → edges stay attached | Scroll to zoom in | Nodes scale; bezier edges stay connected to the correct node handles |
| F4 | Zoom clamp min | Zoom out repeatedly | Canvas stops zooming at 0.25×; further scroll has no effect |
| F5 | Zoom clamp max | Zoom in repeatedly | Canvas stops zooming at 2.5× |
| F6 | Node position in graph coords | Drag node while zoomed to 0.5× → save draft → reload | Node reloads at the same visual position (graph-coord round-trip) |
| F7 | Delete shortcut | Select node → press Delete | Node removed; edges removed; history stack records the state before deletion |
| F8 | Ctrl+Z undo | Add node → Ctrl+Z | Node removed; canvas matches state before add |
| F9 | Ctrl+Z/Ctrl+Shift+Z undo-redo cycle | Add node → Ctrl+Z → Ctrl+Shift+Z | Node removed then re-added |
| F10 | Shortcuts suppressed in inputs | Type in node label field → press Delete | Character deleted from input; node not deleted |
| F11 | Fit view (key 0) | Add several nodes far apart → press 0 | Viewport resets to show all nodes centred |
| F12 | History cap | Perform 55 actions → Ctrl+Z 55 times | Undo stops after ~50 steps; does not crash |

