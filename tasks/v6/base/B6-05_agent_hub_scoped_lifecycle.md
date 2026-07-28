# B6-05: Agent Hub — Scoped Agent Lifecycle

> **Status:** `[ ]`  
> **Owner:** `projects/guardroute`, `gateway/api`  
> **Secondary:** `common/models`, `common/services`  
> **Complexity:** 🟡 Medium (5 subtasks)

---

## Objective

Convert agent management into the **Agent Hub**. Agents live inside a hub, may only retrieve from
collections in ingestion hubs their hub is explicitly linked to, and expose per-hub endpoint slugs. The
external OpenAI-compatible API is updated to address agents by a hub-qualified identifier, and API keys
become optionally hub-scoped.

---

## Acceptance Criteria

- [ ] `AgentDefinition` carries a `NOT NULL hub_id`; `endpoint_slug` is unique per `(hub_id, endpoint_slug)`.
- [ ] Agent CRUD, invoke, batch-invoke, route and stats endpoints are nested under `/hubs/{hub_id}/agents` and guarded by `require_hub(hub_type="agent")`.
- [ ] An agent's collection bindings are stored as qualified references (`{hub_id, collection_id}`) and validated through `hub_resolver` at save **and** at invocation time.
- [ ] Attempting to bind a collection from an unlinked ingestion hub fails with a descriptive `403 HUB_LINK_REQUIRED`.
- [ ] Revoking a hub link causes dependent agent invocations to fail fast with `HUB_LINK_REVOKED` rather than silently reading across the boundary.
- [ ] `agent_invocation_log` records `hub_id` and is only readable within its hub.
- [ ] The external `/v1/chat/completions` endpoint resolves agents by `"{hub_slug}/{agent_slug}"` as the `model` value; the bare-slug form from V5 is removed.
- [ ] A hub-scoped API key may only invoke agents in its own hub; a platform key may invoke any agent its owning user can reach.
- [ ] `GET /v1/models` lists only agents the calling key is authorised for, using the hub-qualified identifier.
- [ ] Flat `/agents` and `/agents/{id}` routes are **removed** from `gateway/api/agent_crud.py` and `agent_invoke.py`.
- [ ] `tests/test_agent_hub.py` covers hub scoping, slug collision across hubs, link enforcement at invoke time, and external API resolution.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-05a | Hub-Scoped Agent Model & Service | [`S6-05a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-05a.md) |
| S6-05b | Agent ↔ Collection Binding via Hub Links | [`S6-05b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-05b.md) |
| S6-05c | Agent Hub CRUD & Invocation Routes | [`S6-05c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-05c.md) |
| S6-05d | Hub-Qualified External API & Scoped API Keys | [`S6-05d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-05d.md) |
| S6-05e | Agent Hub Migration, Legacy Removal & Tests | [`S6-05e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-05e.md) |

---

## References

- [`references/logic/guardroute.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/guardroute.md)
- [`references/logic/agent_endpoints.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/agent_endpoints.md)
- [`references/logic/external_api.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/external_api.md)
- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.3, §5.4
