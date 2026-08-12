# Base Task 14: Workflow Canvas Interactivity, Loading States & Hub Panel Fixes (v7)

## Objective
Harden the Interactive Visual Workflow Builder and its surrounding hub panels with a second round of UX fixes:

1. **Dropdown loading states** — every property-drawer dropdown (agent, retrieval/collection, eval suite, DB credential) must show a loading indicator while its API response is pending, and a graceful empty/error state when it fails.
2. **Canvas interactivity** — the canvas must support pan, zoom, and smooth node movement, plus keyboard shortcuts for common actions (delete, duplicate, save, undo/redo, zoom).
3. **Fullscreen canvas** — the canvas must be able to expand to fullscreen and return.
4. **Members panel crash** — the Members panel throws a runtime error (blank render) because the members API returns `email: null` / `display_name: null` and the frontend calls `m.email.toLowerCase()` unconditionally.
5. **Links panel empty columns** — the Links panel renders empty "Target Hub" / "Target Type" columns because the backend `GET /{hub_id}/links` returns `target_hub_name: null`, `target_hub_type: null`, `target_hub_slug: null` (the denormalized target-hub fields are never populated).

> **Scope note:** Items 1–4 are frontend-only. Item 5 requires a small **backend** change to denormalize the target hub's name/type/slug in the links list endpoint (plus a frontend fallback for robustness).

## Current State (verified against codebase)
- **`frontend/src/components/hubs/workflow/WorkflowEditor.tsx`** — `fetchLinkedHubResources()` fetches agents/collections/eval suites/credentials but has no per-dropdown loading or error state; the dropdowns render empty while requests are in flight.
- **`frontend/src/components/hubs/workflow/WorkflowCanvas.tsx`** — supports node dragging and connection drafting, but has **no** pan, zoom, or keyboard shortcuts.
- **`frontend/src/components/hubs/workflow/WorkflowEditor.tsx`** — no fullscreen toggle for the canvas.
- **`frontend/src/components/hubs/MembersPanel.tsx`** — `filteredMembers` calls `m.email.toLowerCase()` (line ~91) where `email` can be `null` → `TypeError` → blank render. The members API returns `email: null`, `display_name: null`, `avatar_url: null`.
- **`frontend/src/components/hubs/HubLinksPanel.tsx`** — renders `link.target_hub_name` and `link.target_hub_type`, which are `null` from the API → empty columns.
- **`gateway/api/hubs.py`** — `list_hub_links` returns raw `HubLink` ORM rows; `common/schemas/hubs.py::HubLinkRead` declares `target_hub_name` / `target_hub_type` / `target_hub_slug` as `Optional` but they are never populated.
- **`common/services/hub_repository.py`** — `list_links()` selects `HubLink` rows only, with no join to the target `Hub`.

## Subtasks
1. `[x]` `sub_14_01_dropdown_loading_states.md`: Add per-dropdown loading / empty / error states to the workflow editor property drawer (agents, collections, eval suites, DB credentials).
2. `[x]` `sub_14_02_canvas_interactivity_and_shortcuts.md`: Add pan, zoom, and smooth node movement to the canvas, plus keyboard shortcuts (delete, duplicate, save, undo/redo, zoom in/out, fit view).
3. `[x]` `sub_14_03_canvas_fullscreen.md`: Add a fullscreen toggle for the canvas (expand to viewport and restore).
4. `[x]` `sub_14_04_members_panel_null_safety.md`: Make the Members panel null-safe for `email` / `display_name` / `avatar_url` and render a fallback identity.
5. `[x]` `sub_14_05_links_panel_target_hub_denormalization.md`: Populate `target_hub_name` / `target_hub_type` / `target_hub_slug` in the backend links list endpoint (join target hub) and add a frontend fallback so the Links panel never shows empty columns.

## Definition of Done
- All property-drawer dropdowns show a loading spinner while fetching and a clear empty/error state on failure.
- The canvas supports pan, zoom, and node movement; keyboard shortcuts work for delete, duplicate, save, undo/redo, and zoom.
- The canvas can enter and exit fullscreen.
- The Members panel renders without crashing even when `email` / `display_name` are null.
- The Links panel shows the target hub name/type/slug (not empty columns) for outgoing links, and a readable **source** hub name/type for incoming links (currently shows raw `source_hub_id`).

## Test Cases
> Backend tests live in `tests/test_workflow_canvas_interactivity.py` (pytest + in-memory SQLite). Frontend items are manual browser scenarios.

### Backend API Tests (pytest)

| # | Test Name | Scenario | Expected |
|---|-----------|----------|----------|
| B1 | `test_links_list_outgoing_populates_target_hub_name` | Create hub A → hub B → link A→B; call `GET /hubs/A/links` | Each link object has non-null `target_hub_name`, `target_hub_type`, `target_hub_slug` matching hub B |
| B2 | `test_links_list_incoming_populates_target_hub_fields` | Same setup; call `GET /hubs/B/links?direction=incoming` | Returned link has `target_hub_name`/`target_hub_type`/`target_hub_slug` matching hub B (current hub); `source_hub_id` == hub A id |
| B3 | `test_links_target_hub_name_after_hub_rename` | Rename hub B after creating link A→B; call `GET /hubs/A/links` | `target_hub_name` reflects the new name (live join, not cached) |
| B4 | `test_members_list_null_email_user` | Create hub member with `email=None`, `display_name=None`; call `GET /{hub_id}/members` | HTTP 200; member row present with `email: null`, `display_name: null` — no 500 error |
| B5 | `test_members_list_mixed_null_and_real_users` | Hub with 1 null-email member + 1 real member; call `GET /{hub_id}/members` | HTTP 200; array of 2 members; no crash |
| B6 | `test_links_multiple_links_all_populated` | Hub A links to B and C; call `GET /hubs/A/links` | Both link objects have fully populated `target_hub_name`, `target_hub_type`, `target_hub_slug` |
| B7 | `test_links_deleted_target_hub_graceful` | Create link A→B; delete (archive) hub B; call `GET /hubs/A/links` | HTTP 200; link still returned; `target_hub_name` may show archived name or `null` — no 500 crash |
| B8 | `test_links_hub_with_no_outgoing_returns_empty` | Fresh hub with no outgoing links; call `GET /{hub_id}/links` | HTTP 200; returns `[]` with fully formed response (not null) |

### Frontend / Manual Browser Tests

| # | Scenario | Steps | Expected |
|---|----------|-------|----------|
| F1 | Agent dropdown shows loading spinner | Open workflow editor → observe agent node property drawer while network is pending | A spinner or "Loading agents…" placeholder is shown; no empty `<select>` with blank options |
| F2 | Failed resource fetch shows error state | Throttle network to offline → open property drawer | Error message shown (e.g. "Failed to load agents"); not a silent empty list |
| F3 | Empty-hub state distinguishable from loading | Linked agent hub exists but has 0 agents | "No linked agents available" message — distinct from the loading spinner |
| F4 | Canvas pan via space+drag | Hold Space → click-drag on canvas background | Canvas pans smoothly; nodes do not move |
| F5 | Canvas zoom via mouse wheel | Scroll mouse wheel over canvas | Canvas zooms in/out centred on cursor; nodes and edges stay aligned |
| F6 | Keyboard shortcut: Delete node | Select a node → press Delete/Backspace | Selected node and its edges are removed from canvas |
| F7 | Keyboard shortcut: Ctrl+S saves | Make a change → press Ctrl+S | "Saving…" badge appears then "Saved"; network request to `PUT /{wf_id}/draft` fires |
| F8 | Keyboard shortcut: Ctrl+Z undo | Add a node → press Ctrl+Z | Newly added node is removed; previous canvas state restored |
| F9 | Keyboard shortcuts suppressed in inputs | Click inside a node label input field → press Delete | Input character deleted, not the node; no node deletion fires |
| F10 | Fullscreen toggle button | Click the fullscreen expand button in the editor header | Canvas fills the viewport; surrounding hub navigation chrome hidden |
| F11 | Fullscreen exit via Escape | Enter fullscreen → press Escape | Canvas returns to normal editor layout; toggle button resets to "expand" state |
| F12 | Members panel renders with null email/display_name | Hub owner has `email: null`, `display_name: null` in API response | Panel renders without crash; member row shows a fallback label (e.g. "Hub member") and a "?" avatar initial |
