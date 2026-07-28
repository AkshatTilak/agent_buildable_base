# Workflow Builder V5 — Node Types & Graph Parser

> **⚠️ Partially superseded by V6.** Workflow *storage, grouping, versioning and execution* are now
> defined in [`workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md).
> This document remains authoritative **only** for node semantics, the graph parser contract, the state
> schema, and terminal-action constraints — all of which are unchanged in V6.

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

## 1. Overview

V5 extends the Workflow Builder from 4 node types to 11, adding logic blocks, integration nodes, evaluation nodes, and tool nodes. The Graph Parser compiles visual ReactFlow configurations into executable LangGraph StateGraph instances.

The node catalogue, compilation mapping, state schema, sandboxing rules and validation rules below are unchanged in V6. What changed is the surrounding context: V5 assumed a single global workflow and a single flat resource namespace, whereas V6 stores many workflows per Workflow Hub with immutable versions — see [`workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) and [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md).

---

## 2. Node Type Registry

### Core (V2, unchanged)
| Node | Type Key | Handles | Description |
|---|---|---|---|
| ClassifierNode | `classifier` | 1 in, N out (per complexity) | Arch-Router complexity classification |
| AgentNode | `agent` | 1 in, 1 out | Subagent with model + prompt config |
| MultiAgentNode | `multi_agent` | 1 in, 1 out | Drop-in pre-configured agent node for dynamic graph composition |
| RetrievalNode | `retrieval` | 1 in, 1 out | SyntraFlow hybrid search (configurable via Retrieval Strategy) |
| ActionNode | `action` | 1 in, 0 out | Terminal node executing a side-effect (e.g. payload dispatch) |
| FinalMessageNode | `final_message` | 1 in, 0 out | Terminal node returning synthesis output to the user |

### Logic (V5 new)
| Node | Type Key | Handles | Description |
|---|---|---|---|
| IfElseNode | `if_else` | 1 in, 2 out (true/false) | Conditional branching |
| RouterNode | `router` | 1 in, N out | Multi-branch routing |
| TransformNode | `transform` | 1 in, 1 out | Data mapping/formatting |

### Integration (V5 new)
| Node | Type Key | Handles | Description |
|---|---|---|---|
| WebhookNode | `webhook` | 1 in, 1 out | Outbound HTTP POST |
| APICallNode | `api_call` | 1 in, 1 out | REST API call (GET/POST/PUT/DELETE) |

### Evaluation (V5 new)
| Node | Type Key | Handles | Description |
|---|---|---|---|
| EvalNode | `eval` | 1 in, 2 out (pass/fail) | Inline eval run with threshold |

### Tools (V5 new)
| Node | Type Key | Handles | Description |
|---|---|---|---|
| MCPToolNode | `mcp_tool` | 1 in, 1 out | Invoke registered MCP tool |

### Node Resource References (V6 correction)

Where a node references a platform resource, V5 stored a bare id (`agent_id`, `collection_id`). **In V6 every such reference is a qualified object** carrying the owning hub, because ids are only unique within a hub:

```json
{ "type": "agent",      "hub_id": "hub_9f2…", "resource_id": "agt_31c…" }
{ "type": "collection", "hub_id": "hub_4b8…", "resource_id": "col_77a…" }
```

This applies to `AgentNode` / `MultiAgentNode` (agent reference), `RetrievalNode` (collection reference), `EvalNode` (suite reference) and any other node pointing at a hub-scoped resource. References are resolved through `common/services/hub_resolver.py`, which requires an explicit `hub_link` from the workflow's hub to the target hub and re-validates that link at execution time. A bare id in `graph_json` is invalid and fails validation.

---

## 3. Graph Parser Compilation

Each node type maps to a LangGraph construct:

| Node Type | LangGraph Construct |
|---|---|
| `classifier` | `add_node()` + `add_conditional_edges()` |
| `agent` | `add_node()` (async LLM call) |
| `retrieval` | `add_node()` (async vector search) |
| `synthesis` | `add_node()` + `add_edge(_, END)` |
| `if_else` | `add_conditional_edges()` with predicate function |
| `router` | `add_conditional_edges()` with N-way predicate |
| `transform` | `add_node()` (sync data transform) |
| `webhook` | `add_node()` (async HTTP call) |
| `api_call` | `add_node()` (async HTTP call) |
| `eval` | `add_node()` + `add_conditional_edges()` (pass/fail) |
| `mcp_tool` | `add_node()` (async MCP invocation) |

---

## 4. Extended GraphState

```python
class GraphState(TypedDict):
    prompt: str
    session_id: str
    complexity: str
    required_agents: List[str]
    subagent_results: Annotated[List[SubAgentResult], operator.add]
    final_response: str
    token_usage: Dict[str, int]
    # V5 additions:
    webhook_results: Dict[str, Any]
    api_call_results: Dict[str, Any]
    eval_results: Dict[str, Any]
    transform_outputs: Dict[str, Any]
    mcp_tool_results: Dict[str, Any]
    conditional_flags: Dict[str, bool]
```

---

## 5. Security Constraints

### SSRF Protection (Webhook/APICall nodes)
- Block private IP ranges: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `127.0.0.0/8`, `::1`
- Block metadata endpoints: `169.254.169.254`, `metadata.google.internal`
- DNS resolution validated before connection

### Expression Sandboxing (IfElse/Router conditions)
- Custom expressions: restricted to comparisons, boolean operators, string operations
- No function calls, no imports, no attribute access beyond allowed fields
- Implemented via `ast.literal_eval` + safe expression parser

### Transform Sandboxing
- Jinja2 templates rendered via `SandboxedEnvironment`
- No file access, no code execution within templates

---

## 6. Validation Rules (Graph Parser)

1. All graphs must have exactly one entry point (ClassifierNode or direct start)
2. **Terminal Constraint (Rule 8):** Every valid graph path MUST conclude in either an `ActionNode` or a `FinalMessageNode`. The parser will reject any graph with dangling endpoints or fallback to legacy Synthesis nodes if improperly configured.
3. Cycle detection via Kahn's algorithm (handles branching)
4. IfElse/Router nodes must have all branches connected
5. EvalNode must have both pass and fail branches connected
6. Webhook/APICall nodes must have valid URL configurations
7. MCPToolNode must reference existing tools in the cache
8. *(V6)* Every agent/collection/suite reference must be a qualified `{type, hub_id, resource_id}` object resolvable through an existing hub link; a revoked or missing link fails validation on publish and fails the run with `HUB_LINK_REVOKED` at execution time.

---

## 7. Node Palette Categories

The WorkflowCanvas palette auto-populates from platform data:

| Category | Source | Dynamic |
|---|---|---|
| Core | Static list | No |
| Agents | `GET /hubs/{hub_id}/agents` on each linked agent hub (active only) | Yes |
| Logic | Static list (IfElse, Router, Transform) | No |
| Integrations | Static list (Webhook, APICall) | No |
| Evaluation | `GET /hubs/{hub_id}/eval/suites` on each linked eval hub | Yes |
| Tools | `GET /api/mcp/tools` (enabled only — MCP Registry stays platform-level) | Yes |

> The flat V5 endpoints `GET /api/agents` and `GET /api/evalops/suites` were removed in V6. Dynamic palette categories now enumerate only the hubs the current workflow hub is linked to, grouped by source hub.

---

## 8. Runtime Node Implementations

All located in `projects/guardroute/src/nodes/`:
- `conditional_evaluator.py` — IfElse + Router condition evaluation
- `webhook_executor.py` — HTTP call with retry/timeout/SSRF protection
- `api_call_executor.py` — REST API with auth + response mapping
- `eval_executor.py` — Inline evaluation with threshold
- `mcp_tool_executor.py` — MCP tool invocation wrapper
- `transform_executor.py` — Template rendering + field extraction
