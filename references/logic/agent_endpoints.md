# Agent Endpoints & Smart Routing Architecture

> **Source:** V5 Planning (B5-02)  
> **Last Updated:** 2026-07-23 (V5 Platform Maturity - S5-02d & S5-02e Complete)

---

## 1. Overview

The Agent Endpoints & Routing API (`gateway/api/agent_invoke.py` & `gateway/api/agent_crud.py`) exposes unified REST and SSE endpoints for invoking defined agents, managing activation states (`is_active`), generating friendly URL slugs (`endpoint_slug`), performing smart prompt routing, and logging invocation telemetry.

> **V6 UPDATE — agents are hub-scoped.** Every agent belongs to exactly one `agent` hub. All routes in
> this document are nested under `/hubs/{hub_id}/...` and resolved through the `require_hub()`
> dependency; the flat `/api/agents/*` invocation routes from V5 are **removed**, not aliased.
> See [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md).

### 1.1 Hub scoping rules

- `agent_definitions.endpoint_slug` is unique per **`(hub_id, endpoint_slug)`**, not globally
  (`hubs.md` §3.7). Two hubs may each own an agent called `support-bot`; they are distinct agents.
- Resolving an agent by slug is therefore only meaningful **within a hub**. A slug lookup without a
  `hub_id` is a defect.
- Every query, update and delete filters by `hub_id` in addition to the resource id
  (`hubs.md` §5.3). A bare primary-key lookup is an IDOR vulnerability.
- A caller who is not a member of the hub receives `404 Hub not found` — never `403` — so hub
  existence cannot be enumerated (`hubs.md` §5.2).
- Minimum hub roles: read routes require `viewer`; create/update/invoke require `contributor`;
  delete requires `maintainer`.

---

## 2. API Endpoints

All paths below are hub-nested. `{hub_id}` must resolve to a hub of type `agent`; any other hub type
returns `404`.

### Agent Definition CRUD & Lifecycle
- `GET /hubs/{hub_id}/agents`: List the agents **in this hub only**.
- `POST /hubs/{hub_id}/agents`: Create a new agent definition. Auto-generates an `endpoint_slug` from the agent `name` (slugified, lowercase, hyphenated), unique within the hub.
- `PUT /hubs/{hub_id}/agents/{agent_id}`: Update agent configurations. Auto-updates `endpoint_slug` if `name` or `endpoint_slug` changes, re-checking uniqueness against `(hub_id, endpoint_slug)`.
- `PATCH /hubs/{hub_id}/agents/{agent_id}/toggle`: Toggle activation state (`is_active`) of an agent between active and inactive.
- `DELETE /hubs/{hub_id}/agents/{agent_id}`: Remove agent definition.

### Single Agent Invocation
- `POST /hubs/{hub_id}/agents/{agent_id}/invoke`
- Accepts `AgentInvokeRequest`: `{ prompt, session_id, conversation_history, temperature, max_tokens, stream }`
- Resolves the agent definition by either `id` (UUID) or `endpoint_slug`, **always scoped to `hub_id`**.
- **Activation Check:** If `agent.is_active` is `False`, returns `403 Forbidden` (`Agent is currently inactive and cannot be invoked`).
- Resolves system prompt and hyperparameters from `AgentDefinition`.
- **Collection access check:** any collection the agent retrieves from is resolved through
  `common/services/hub_resolver.py`. If the collection lives in another hub, an active `hub_link`
  (`agent → ingestion`, `access_level` ≥ `read`) must exist from this hub to that hub. The link is
  re-validated **at invocation time**, not only when the agent was saved; a revoked or missing link
  fails the invocation with `HUB_LINK_REVOKED` (`403`).
- Invokes model via LiteLLM `completion_with_fallback()`.
- Triggers fire-and-forget background invocation logging (`AgentInvocationLog`, stamped with `hub_id`).

### Batch Invocation
- `POST /hubs/{hub_id}/agents/{agent_id}/invoke/batch`
- Accepts `AgentBatchInvokeRequest`: `{ prompts: [str] }` (capped at 20 prompts).
- Executes prompts sequentially against the specified active agent.

### Unified Smart Router
- `POST /hubs/{hub_id}/route`
- Accepts `RouteRequest`: `{ prompt, agent_id, model_id, routing_strategy, session_id, stream }`
- Strategies (all agent candidate sets are restricted to agents **in this hub**):
  1. `direct_agent`: If `agent_id` supplied, routes directly to agent (verifying `is_active`).
  2. `direct_model`: If `model_id` supplied, routes directly to LiteLLM model.
  3. `round_robin`: Cycle through the hub's active agents (`is_active == True`) using an atomic counter.
  4. `auto`: Matches prompt against the hub's active (`is_active == True`) Agent names/roles, or falls through to default completion model (`gemini/gemini-3.5-flash`).

### Analytics & Invocation Stats
- `GET /hubs/{hub_id}/agents/{agent_id}/stats`
- Returns `AgentStatsResponse`: `{ agent_id, total_invocations, avg_latency_ms, total_input_tokens, total_output_tokens, status_counts, last_used }`
- Aggregates only `agent_invocation_log` rows belonging to this hub.

### Removed in V6

`/api/agents`, `/api/agents/{agent_id_or_slug}/invoke`, `/api/agents/{agent_id}/invoke/batch`,
`/api/route` and `/api/agents/{agent_id}/stats` no longer exist. There are no back-compat aliases — the
cutover is hard, per `hubs.md` §6. External OpenAI-compatible access uses the hub-qualified
`"{hub_slug}/{agent_slug}"` model identifier instead; see
[`external_api.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/external_api.md).

---

## 3. Database Schema

- `agent_definitions`: Extended with:
  - `hub_id` (`String(36)`, FK `hubs.id`, **NOT NULL**, indexed): owning agent hub.
  - `is_active` (`Boolean`, default `True`, indexed): Controls whether agent accepts invocations and participates in routing.
  - `endpoint_slug` (`String(100)`, indexed): URL-safe identifier generated from agent name.
    **`UNIQUE (hub_id, endpoint_slug)`** in V6 — the V5 global unique constraint is dropped, so two
    hubs may both own `support-bot`.
- `agent_invocation_log`: Stores `id`, **`hub_id`** (FK `hubs.id`, **NOT NULL**, indexed), `agent_id`, `user_id`, `prompt`, `response`, `model_used`, `input_tokens`, `output_tokens`, `latency_ms`, `status`, `route_decision`, `metadata_json`, `created_at`.
  Invocation logs are readable **only within their own hub**: every list, detail and aggregate query
  filters by `hub_id`, and a caller outside the hub sees `404`. There is no cross-hub invocation view
  other than the platform `/admin/audit` surface.
