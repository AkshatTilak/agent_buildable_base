# Workflow Builder V5 — Node Types & Graph Parser

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

## 1. Overview

V5 extends the Workflow Builder from 4 node types to 11, adding logic blocks, integration nodes, evaluation nodes, and tool nodes. The Graph Parser compiles visual ReactFlow configurations into executable LangGraph StateGraph instances.

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

---

## 7. Node Palette Categories

The WorkflowCanvas palette auto-populates from platform data:

| Category | Source | Dynamic |
|---|---|---|
| Core | Static list | No |
| Agents | `GET /api/agents` (active only) | Yes |
| Logic | Static list (IfElse, Router, Transform) | No |
| Integrations | Static list (Webhook, APICall) | No |
| Evaluation | `GET /api/evalops/suites` | Yes |
| Tools | `GET /api/mcp/tools` (enabled only) | Yes |

---

## 8. Runtime Node Implementations

All located in `projects/guardroute/src/nodes/`:
- `conditional_evaluator.py` — IfElse + Router condition evaluation
- `webhook_executor.py` — HTTP call with retry/timeout/SSRF protection
- `api_call_executor.py` — REST API with auth + response mapping
- `eval_executor.py` — Inline evaluation with threshold
- `mcp_tool_executor.py` — MCP tool invocation wrapper
- `transform_executor.py` — Template rendering + field extraction
