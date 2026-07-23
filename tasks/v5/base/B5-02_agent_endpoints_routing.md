# B5-02: Agent Endpoints & Routing API

> **Status:** `[x]`  
> **Owner:** `gateway`, `projects/guardroute`  
> **Secondary:** `common/schemas`  
> **Complexity:** 🟡 Medium (5 subtasks)

---

## Objective

Give every created agent a live, callable endpoint. Provide a unified routing API that can direct queries to specific agents, auto-route via the classifier, or target a model directly. Track all invocations with logging and surface stats in the dashboard.

---

## Acceptance Criteria

- [x] `POST /api/agents/{agent_id}/invoke` resolves agent config and executes LLM completion
- [x] Streaming SSE support on agent invoke
- [x] If agent has tools → invoke tool pipeline (MCP, code sandbox, search)
- [x] `POST /api/route` accepts `agent_id`, `model_id`, or `routing_strategy: "auto"` to classify and route
- [x] Agent invocations logged to `agent_invocation_log` table
- [x] Agent CRUD extended with `is_active` toggle, auto-generated `endpoint_slug`, stats endpoint
- [x] Frontend shows active toggle, endpoint URL, inline "Test Agent" panel, and invocation stats

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-02a | Agent Invocation Endpoint | [`S5-02a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-02a.md) |
| S5-02b | Unified Routing Endpoint | [`S5-02b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-02b.md) |
| S5-02c | Agent Invocation Logging | [`S5-02c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-02c.md) |
| S5-02d | Agent CRUD Enhancements | [`S5-02d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-02d.md) |
| S5-02e | Frontend: Agent Invoke Panel & Stats | [`S5-02e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-02e.md) |
