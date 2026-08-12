# Subtask 14_03: Canvas Fullscreen Mode

## Objective
Allow the workflow canvas to expand to fullscreen and return, so users can work on large graphs without the surrounding editor chrome.

## Root Cause
- `WorkflowCanvas.tsx` is rendered inside a fixed-height (`h-[650px]`) grid column in `WorkflowEditor.tsx` with no fullscreen capability.

## Tasks
1. `[x]` **Add a fullscreen toggle** — add an `<Maximize2>` / `<Minimize2>` Lucide icon button to the editor canvas toolbar. Clicking it toggles fullscreen mode.
2. `[x]` **Implement expand/restore** — use a **CSS fixed-overlay** approach: set the canvas container to `position: fixed; inset: 0; z-index: 100` rather than the browser Fullscreen API, to avoid cross-browser quirks and to keep the React event system intact. The right-side property drawer is **excluded** from fullscreen (it slides over the canvas or becomes a floating panel) to avoid wasting horizontal space.
3. `[x]` **Preserve pan/zoom/node state** — viewport `{ x, y, zoom }` and node positions must not change when toggling fullscreen; the canvas simply expands/contracts its CSS bounds around the same graph state.
4. `[x]` **Keyboard exit** — listen for `Escape` keydown to exit fullscreen mode (same guard as other shortcuts: skip if focus is in an input). Sync the toggle button icon accordingly.

> **Architecture note:** The browser Fullscreen API (`element.requestFullscreen()`) is a valid alternative but may require workarounds for React portals and z-index stacking. The CSS overlay approach is simpler and consistent with the existing layout. Document the chosen approach in a code comment.

## Definition of Done
- The canvas can enter and exit fullscreen via a button.
- Pan/zoom/node state is preserved across the toggle.
- The toggle button reflects the current fullscreen state (Maximize vs Minimize icon).
- Pressing Escape exits fullscreen.

## Test Cases

### Frontend Manual
| # | Scenario | Steps | Expected |
|---|----------|-------|----------|
| F1 | Enter fullscreen | Click the Maximize button | Canvas fills the full viewport (100vw × 100vh); hub shell navigation is hidden |
| F2 | Exit fullscreen via button | While fullscreen, click the Minimize button | Canvas returns to normal h-[650px] in-editor layout |
| F3 | Exit fullscreen via Escape | While fullscreen, press Escape | Same as F2 |
| F4 | Viewport state preserved | Pan to offset (200, 100) at zoom 1.5× → enter fullscreen → exit fullscreen | Pan offset and zoom level unchanged |
| F5 | Node positions unchanged | Move a node → enter fullscreen → exit fullscreen | Node is at the same graph position |
| F6 | Button icon syncs | Enter fullscreen → verify icon is Minimize; exit → verify icon is Maximize | Icon correctly reflects state in both cases |
| F7 | Keyboard shortcut suppressed in inputs | While fullscreen and a node input is focused → press Escape | Escape closes the input focus (default browser behaviour), does NOT exit fullscreen |

