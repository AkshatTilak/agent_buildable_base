# Subtask 13_03: Cross-Hub Resource Fetching in the Editor

## Objective
Make the workflow editor's property drawer dropdowns (agent, retrieval/collection, eval suite, DB credential) populate from **linked** hubs instead of the current workflow hub, so cross-hub links actually work.

## Root Cause
- `WorkflowEditor.tsx` fetched agents via `api.agents.list(hubId)` where `hubId` is the **workflow** hub, but the backend `GET /hubs/{hub_id}/agents` requires `hub_type="agent"` → fails.
- Same for collections via `api.ingestion.collections.list(hubId)` (requires an **ingestion** hub) and eval suites / DB credentials.

## Tasks
1. `[x]` **Discover linked hubs** — in `WorkflowEditor.tsx`, add `fetchLinkedHubResources()` that calls `api.hubs.links.list(hubId)` to get outgoing links, then builds a set of hub ids = `{ currentHubId } ∪ { link.target_hub_id }`.
2. `[x]` **Fetch resources per hub** — for each hub id, fetch agents (`api.agents.list`), collections (`api.ingestion.collections.list` → `res.collections`), eval suites (`api.evals.suites.list`), and DB credentials (`api.dbCredentials.list`), merging results. Wrap each call in try/catch so a wrong-hub-type call is silently skipped.
3. `[x]` **Wire into load** — call `fetchLinkedHubResources()` from `fetchWorkflowAndHubResources()` after the graph loads.

## Definition of Done
- Agent dropdown lists agents from linked agent hubs (and the current hub if it is an agent hub).
- Retrieval dropdown lists collections from linked ingestion hubs.
- Eval suite and DB credential dropdowns list resources from linked hubs.
- Resources from multiple linked hubs of the same type are merged and **deduplicated by `id`** so no resource appears twice.
- A hub-type mismatch error (e.g. calling `api.agents.list` on an ingestion hub) is silently swallowed — it never crashes or surfaces in the UI.

## Test Cases

### Backend (`tests/test_workflow_canvas_fixes.py`)
| # | Test | Expected |
|---|------|----------|
| B1 | `test_links_direction_outgoing_returns_linked_hub_ids` | Workflow hub linked to agent hub A and ingestion hub B → `GET /hubs/{wf_hub_id}/links` → returns 2 links with correct `target_hub_id` values |
| B2 | `test_agents_list_on_workflow_hub_returns_403_or_empty` | `GET /hubs/{wf_hub_id}/agents` (wrong hub type) → HTTP 403 or 400 (not 500) |

### Frontend Manual
| # | Scenario | Expected |
|---|----------|----------|
| F1 | Agents from linked agent hub | Workflow hub → linked to agent hub with 3 agents → open Agent node drawer | All 3 agents in dropdown |
| F2 | Collections from linked ingestion hub | Linked ingestion hub with 2 collections → open Retrieval node drawer | Both collections in dropdown |
| F3 | Wrong-hub silently skipped | No linked agent hub → open Agent node drawer | "No linked agents available" — no JS error |
| F4 | Dedup: same hub linked twice | Agent hub A linked from workflow hub twice (hypothetical) → open Agent drawer | Each agent appears only once |
| F5 | Resources from multiple hubs merged | Two linked agent hubs (A: 2 agents, B: 1 agent) → open Agent drawer | All 3 agents shown |

