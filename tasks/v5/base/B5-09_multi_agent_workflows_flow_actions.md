# B5-09: Multi-Agent Workflows & Strict Flow Terminations

> **Status:** `[x]`  
> **Owner:** `projects/guardroute`  
> **Secondary:** `frontend`  
> **Complexity:** 🔴 High (4 subtasks)

---

## Objective

Evolve GuardRoute and the Workflow Builder to support dynamic multi-agent orchestrations, passing state between specialized agents and logic blocks, and enforcing Rule 8 (strict terminal node constraints: ActionNode or FinalMessageNode).

---

## Acceptance Criteria

- [x] `MultiAgentNode` executor supports state passing and tool calls across multiple specialized agents
- [x] `ActionNode` (side-effects like webhooks/DB writes) and `FinalMessageNode` (LLM synthesis output) executors implemented
- [x] Graph Parser V5 enforces Rule 8 (terminal constraint), rejecting dangling nodes or invalid graph structures
- [x] ReactFlow canvas and Property Drawers support MultiAgent, Action, and FinalMessage node components

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-09a | MultiAgentNode Runtime Executor & State Transfer | [`S5-09a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-09a.md) |
| S5-09b | Terminal Node Executors (Action & FinalMessage) | [`S5-09b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-09b.md) |
| S5-09c | Graph Parser V5 Enforcement & Rule 8 Validation | [`S5-09c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-09c.md) |
| S5-09d | Frontend: MultiAgent, Action & FinalMessage Nodes | [`S5-09d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-09d.md) |
