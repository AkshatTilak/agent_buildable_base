# B5-06: Workflow Builder Enhancements

> **Status:** `[ ]`  
> **Owner:** `projects/guardroute`, `frontend`  
> **Secondary:** `projects/evalops` (EvalNode)  
> **Complexity:** 🔴 High (7 subtasks)

---

## Objective

Expand the visual Workflow Builder from its current 4-node-type system into a comprehensive flow engine with logic blocks (IfElse, Router), integration nodes (Webhook, API Call), evaluation nodes, MCP tool nodes, and data transformation nodes. The node palette should dynamically populate as new agents, MCP tools, and eval suites are added to the platform.

---

## Current Node Types (V2)
- `ClassifierNode` — Arch-Router complexity classification
- `AgentNode` — Subagent with model/prompt config
- `RetrievalNode` — SyntraFlow hybrid vector search
- `SynthesisNode` — Gather/aggregation node

## New Node Types (V5)

| Node | Category | Description |
|---|---|---|
| **IfElseNode** | Logic | Conditional branching with true/false output handles |
| **RouterNode** | Logic | Multi-branch routing based on rules or classification |
| **TransformNode** | Logic | Data mapping, field extraction, template rendering |
| **WebhookNode** | Integration | Outbound HTTP POST to external URLs |
| **APICallNode** | Integration | REST API call (GET/POST/PUT/DELETE) with auth |
| **EvalNode** | Evaluation | Triggers eval run within workflow, outputs pass/fail |
| **MCPToolNode** | Tools | Invokes any registered MCP tool |

---

## Acceptance Criteria

- [ ] All 7 new node types have frontend React components with proper handles and PropertyDrawer config
- [ ] IfElseNode supports conditions on: complexity, agent_output, metadata fields, regex, custom expressions
- [ ] WebhookNode has URL, headers, body template with `{{variable}}` interpolation, timeout, retry
- [ ] APICallNode supports GET/POST/PUT/DELETE with auth (None/Bearer/API Key)
- [ ] EvalNode lets user select suite, framework, metrics, and threshold for pass/fail
- [ ] MCPToolNode dropdown populates from registered MCP tools
- [ ] RouterNode supports N-way branching with condition → target mapping
- [ ] TransformNode supports Jinja2-style template rendering
- [ ] Graph Parser V5 handles all new node types → compiles to executable LangGraph
- [ ] SSRF protection on WebhookNode/APICallNode (block private IP ranges)
- [ ] Node palette auto-populates categorized sections: Agents, Logic, Integrations, Evals, Tools

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-06a | IfElseNode | [`S5-06a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-06a.md) |
| S5-06b | WebhookNode | [`S5-06b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-06b.md) |
| S5-06c | APICallNode | [`S5-06c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-06c.md) |
| S5-06d | EvalNode & MCPToolNode | [`S5-06d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-06d.md) |
| S5-06e | RouterNode & TransformNode | [`S5-06e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-06e.md) |
| S5-06f | Graph Parser V5 | [`S5-06f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-06f.md) |
| S5-06g | Dynamic Node Palette | [`S5-06g.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-06g.md) |
