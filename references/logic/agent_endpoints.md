# Agent Endpoints & Smart Routing Architecture

> **Source:** V5 Planning (B5-02)  
> **Last Updated:** 2026-07-23 (V5 Platform Maturity - S5-02d & S5-02e Complete)

---

## 1. Overview

The Agent Endpoints & Routing API (`gateway/api/agent_invoke.py` & `gateway/api/agent_crud.py`) exposes unified REST and SSE endpoints for invoking defined agents, managing activation states (`is_active`), generating friendly URL slugs (`endpoint_slug`), performing smart prompt routing, and logging invocation telemetry.

---

## 2. API Endpoints

### Agent Definition CRUD & Lifecycle
- `GET /api/agents`: List all registered agents.
- `POST /api/agents`: Create a new agent definition. Auto-generates unique `endpoint_slug` from agent `name` (slugified, lowercase, hyphenated).
- `PUT /api/agents/{agent_id}`: Update agent configurations. Auto-updates `endpoint_slug` if `name` or `endpoint_slug` changes.
- `PATCH /api/agents/{agent_id}/toggle`: Toggle activation state (`is_active`) of an agent between active and inactive.
- `DELETE /api/agents/{agent_id}`: Remove agent definition.

### Single Agent Invocation
- `POST /api/agents/{agent_id_or_slug}/invoke`
- Accepts `AgentInvokeRequest`: `{ prompt, session_id, conversation_history, temperature, max_tokens, stream }`
- Resolves agent definition by either `id` (UUID) or `endpoint_slug`.
- **Activation Check:** If `agent.is_active` is `False`, returns `403 Forbidden` (`Agent is currently inactive and cannot be invoked`).
- Resolves system prompt and hyperparameters from `AgentDefinition`.
- Invokes model via LiteLLM `completion_with_fallback()`.
- Triggers fire-and-forget background invocation logging (`AgentInvocationLog`).

### Batch Invocation
- `POST /api/agents/{agent_id}/invoke/batch`
- Accepts `AgentBatchInvokeRequest`: `{ prompts: [str] }` (capped at 20 prompts).
- Executes prompts sequentially against the specified active agent.

### Unified Smart Router
- `POST /api/route`
- Accepts `RouteRequest`: `{ prompt, agent_id, model_id, routing_strategy, session_id, stream }`
- Strategies:
  1. `direct_agent`: If `agent_id` supplied, routes directly to agent (verifying `is_active`).
  2. `direct_model`: If `model_id` supplied, routes directly to LiteLLM model.
  3. `round_robin`: Cycle through active agents (`is_active == True`) using atomic counter.
  4. `auto`: Matches prompt against active (`is_active == True`) Agent names/roles, or falls through to default completion model (`gemini/gemini-3.5-flash`).

### Analytics & Invocation Stats
- `GET /api/agents/{agent_id}/stats`
- Returns `AgentStatsResponse`: `{ agent_id, total_invocations, avg_latency_ms, total_input_tokens, total_output_tokens, status_counts, last_used }`

---

## 3. Database Schema

- `agent_definitions`: Extended with:
  - `is_active` (`Boolean`, default `True`, indexed): Controls whether agent accepts invocations and participates in routing.
  - `endpoint_slug` (`String(100)`, unique, indexed): URL-safe identifier generated from agent name.
- `agent_invocation_log`: Stores `id`, `agent_id`, `user_id`, `prompt`, `response`, `model_used`, `input_tokens`, `output_tokens`, `latency_ms`, `status`, `route_decision`, `metadata_json`, `created_at`.
