# Subtask 13_04: Links Panel 404 Fix (dependents endpoint)

## Objective
Fix the Links panel (`/hubs/workflow/{hubId}/links`) which returns 404 "Not Found" because it calls a nonexistent backend endpoint.

## Root Cause
- `HubLinksPanel.tsx` calls `api.hubs.links.dependents(hub.id)` to load incoming links.
- `frontend/src/services/api.ts` mapped `dependents` to `GET /api/hubs/{hubId}/dependents`, which **does not exist** → 404.
- The backend exposes incoming links via `GET /{hub_id}/links?direction=incoming` (see `gateway/api/hubs.py` `list_hub_links`).

## Tasks
1. `[x]` **Fix the API mapping** — in `frontend/src/services/api.ts`, change `dependents` to call `GET /api/hubs/{hubId}/links?direction=incoming` (returns `HubLink[]`).
2. `[x]` **Verify the panel** — confirm `HubLinksPanel` renders outgoing links (from `api.hubs.links.list`) and incoming links (from the fixed `dependents`) without a 404.
3. `[x]` **Incoming links table header** — the incoming links table currently shows a "Source Hub ID" raw UUID column header; this should be renamed to "Source Hub" and display the hub name if available (from `hubsById` store lookup) with the raw ID as a fallback tooltip.

> **Note:** The incoming links table in `HubLinksPanel.tsx` (line ~277) renders `{link.source_hub_id}` as a raw UUID. Until sub_14_05 populates source-hub name fields from the backend, the frontend must use `hubsById[link.source_hub_id]?.name ?? link.source_hub_id` as the display value.

## Definition of Done
- The Links panel loads without a 404 and shows both outgoing and incoming links.
- Incoming links show a readable source hub name (from store lookup or raw ID fallback) — not just a bare UUID.

## Test Cases

### Backend (`tests/test_workflow_canvas_fixes.py`)
| # | Test | Expected |
|---|------|----------|
| B1 | `test_links_direction_incoming` | Setup: hub A → hub B; call `GET /hubs/B/links?direction=incoming` | HTTP 200; returns 1 link with `source_hub_id == A`, `target_hub_id == B` |
| B2 | `test_links_direction_incoming_empty` | Fresh hub, no incoming links; call `GET /{hub_id}/links?direction=incoming` | HTTP 200; returns `[]` |
| B3 | `test_dependents_404_does_not_exist` | `GET /api/hubs/{hub_id}/dependents` | HTTP 404 (confirms the old endpoint never existed — we call the correct one now) |

### Frontend Manual
| # | Scenario | Expected |
|---|----------|----------|
| F1 | Links panel — no 404 | Navigate to `/hubs/workflow/{hubId}/links` | No 404 in Network tab; both table sections render |
| F2 | Incoming links show source hub name | Another hub has an incoming link to this hub; that hub is loaded in store | Source Hub column shows hub name, not raw UUID |
| F3 | Incoming links fallback to ID | Source hub not in store | Raw `source_hub_id` shown (no crash) |

