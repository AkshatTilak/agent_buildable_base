# Subtask 14_05: Links Panel Target Hub Denormalization

## Objective
Fix the Links panel showing empty "Target Hub" / "Target Type" columns by populating the denormalized target-hub fields in the backend links list endpoint, with a frontend fallback for robustness.

## Root Cause
- `HubLinksPanel.tsx` renders `link.target_hub_name` and `link.target_hub_type`, but `GET /{hub_id}/links` returns `target_hub_name: null`, `target_hub_type: null`, `target_hub_slug: null`.
- `gateway/api/hubs.py::list_hub_links` returns raw `HubLink` ORM rows; `common/schemas/hubs.py::HubLinkRead` declares `target_hub_name` / `target_hub_type` / `target_hub_slug` as `Optional` but they are never populated.
- `common/services/hub_repository.py::list_links()` selects `HubLink` rows only, with no join to the target `Hub`.

## Tasks
1. `[x]` **Backend: join target hub for outgoing links** — in `gateway/api/hubs.py::list_hub_links` (`direction == "outgoing"` branch), after fetching `HubLink` rows via `list_links()`, resolve each link's `target_hub_id` to a `Hub` row and set `target_hub_name`, `target_hub_type`, `target_hub_slug` on the returned `HubLinkRead`. Use a **live join** (fetch hub at query time) rather than caching to avoid stale data after hub renames. Add a helper function `_resolve_hub_fields(db, hub_id) → dict` in `gateway/api/hubs.py` that returns `{ name, hub_type, slug }` or `{ None, None, None }` if the hub is not found.
2. `[x]` **Backend: populate target hub fields for incoming links** — in the `direction == "incoming"` branch, the "target" hub is the current hub (`ctx.hub`). Set `target_hub_name = ctx.hub.name`, `target_hub_type = ctx.hub.hub_type`, `target_hub_slug = ctx.hub.slug` on each returned `HubLinkRead`. No join is needed (current hub is already in context).
3. `[x]` **Backend: add source_hub_name to HubLinkRead schema** — add `source_hub_name: Optional[str] = None` and `source_hub_type: Optional[HubType] = None` to `HubLinkRead` in `common/schemas/hubs.py`. Populate these from the `source_hub_id` join in the incoming-links branch (source = the hub that created the link, i.e. join `HubLink.source_hub_id → Hub`).
4. `[x]` **Frontend: render source hub name for incoming links** — in `HubLinksPanel.tsx`, replace the "Source Hub ID" column (raw UUID) with a "Source Hub" column showing `link.source_hub_name ?? hubsById[link.source_hub_id]?.name ?? link.source_hub_id`. Change the column header from "Source Hub ID" to "Source Hub".
5. `[x]` **Frontend fallback for outgoing links** — when `target_hub_name` is still null (backend not yet updated or archived hub), fall back to `hubsById[link.target_hub_id]?.name ?? link.target_hub_id`.

> **Schema change note:** `source_hub_name` and `source_hub_type` are new fields on `HubLinkRead`. They are `Optional` so existing callers that don't render them are unaffected. No DB migration is needed (fields are not persisted; they are computed at query time).

## Definition of Done
- `GET /{hub_id}/links` (outgoing) returns populated `target_hub_name` / `target_hub_type` / `target_hub_slug`.
- `GET /{hub_id}/links?direction=incoming` returns populated `target_hub_name`/`target_hub_type`/`target_hub_slug` (current hub) and `source_hub_name`/`source_hub_type` (source hub).
- The Links panel outgoing table shows the target hub name/type/slug — not empty columns.
- The Links panel incoming table shows the source hub name/type — not a raw UUID.

## Test Cases

### Backend (`tests/test_workflow_canvas_interactivity.py`)
| # | Test | Expected |
|---|------|----------|
| B1 | `test_links_outgoing_populates_target_hub_name` | Hub A → Hub B link; `GET /hubs/A/links` | `target_hub_name == hub_B.name`, `target_hub_type == hub_B.hub_type`, `target_hub_slug == hub_B.slug`; all non-null |
| B2 | `test_links_incoming_populates_target_hub_fields` | Same setup; `GET /hubs/B/links?direction=incoming` | `target_hub_name == hub_B.name` (current hub); `source_hub_name == hub_A.name`; `source_hub_type == hub_A.hub_type` |
| B3 | `test_links_target_hub_name_reflects_rename` | Rename hub B → `GET /hubs/A/links` | `target_hub_name` equals new name (live join, not stale) |
| B4 | `test_links_archived_target_hub_no_crash` | Archive hub B → `GET /hubs/A/links` | HTTP 200; link returned; `target_hub_name` may be the archived name or null — no 500 |
| B5 | `test_links_multiple_links_all_fields_populated` | Hub A → B and A → C; `GET /hubs/A/links` | Both link objects have non-null `target_hub_name` |

### Frontend Manual
| # | Scenario | Steps | Expected |
|---|----------|-------|----------|
| F1 | Outgoing links show hub name | Create a link from workflow hub to an agent hub | "Target Hub" column shows agent hub's name (not a UUID) |
| F2 | Incoming links show source hub name | Navigate to the agent hub's Links panel | "Source Hub" column shows workflow hub's name (not raw UUID) |
| F3 | Frontend fallback when backend is null | Simulate `target_hub_name: null` from API; hub is in store | Column shows the hub name looked up from `hubsById` store |
| F4 | Frontend fallback when hub not in store | Simulate `target_hub_name: null` and hub not in `hubsById` | Column shows raw hub ID; no crash or empty cell |

