# GuardRoute — Decision Routing & Orchestration Architecture

> **V6 Update — Hub Scoping.** GuardRoute now backs two hub types — the **Agent Hub** (agent definitions, endpoints, invocation logs) and the **Workflow Hub** (many versioned workflows per hub) — instead of one flat global agent and workflow plane.
> All resources described below are scoped by `hub_id`. See
> [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md)
> for the canonical tenancy model.

> **Source:** Migrated from `requirements/guardroute.md`
> **Last Updated:** 2026-07-28 (V6 hub scoping)

GuardRoute classifies query complexity, coordinates parallel subagents using LangGraph, implements fallback routing via LiteLLM, publishes diagnostic logs to Kafka, and manages multi-turn conversations. Model selection is configurable via the Model Registry (see `references/logic/model_registry.md`).

---

## 0. Hub-Scoped Storage (V6)

### Agent Hub tables

| Table | Change |
|---|---|
| `agent_definitions` | **`NOT NULL hub_id`** FK `hubs.id` (`hub_type = 'agent'`), indexed |
| `agent_invocation_log` | **`NOT NULL hub_id`**, indexed |

* `endpoint_slug` uniqueness moves from global `UNIQUE` to **`UNIQUE (hub_id, endpoint_slug)`** — two agent hubs may each expose a `support-triage` endpoint.
* Every agent read and write filters by `hub_id`; resolving an agent by primary key alone is forbidden (see [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §5.3).

### Collection bindings are qualified references

An agent no longer stores bare collection ids. Each binding is a **qualified reference**:

```json
{ "hub_id": "hub_4b8…", "collection_id": "col_77a…" }
```

Bindings are validated through `common/services/hub_resolver.py` **twice**:

1. **At save time** — the agent hub must hold an `agent → ingestion` `hub_link` to the referenced hub, otherwise the save fails `422`.
2. **At invocation time** — re-checked before retrieval runs; a revoked link fails the invocation with `HUB_LINK_REVOKED` rather than silently reading across a tenancy boundary.

### Workflow storage

Workflow persistence is reworked for V6: many workflows per workflow hub, immutable `workflow_versions`,
a draft/publish lifecycle, and persisted `workflow_runs` with per-node traces. The detail is **not**
restated here — see
[`references/logic/workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md).
Node semantics, the graph parser and terminal constraints below are unchanged.

---

## 1. Intent & Complexity Classifier ✅

### Configurable Model Routing
- Route user prompt to inference server `/infer/classify` endpoint.
- Default local model: `Arch-Router-1.5B` (GGUF).
- Alternative: `Semantic Router` (zero VRAM — uses embedding model).
- Cloud option: `Gemini 3.5 Flash` with structured JSON output.

### Complexity Categorization
- **Simple:** Route directly to Synthesis Agent.
- **Medium:** Trigger a single subagent (e.g., RAG retrieval).
- **Complex:** Trigger Scatter-Gather graph for parallel subagent execution.

Classification behaviour is unchanged in V6. What changes is **resolution**: the subagents a classifier may fan out to are drawn from the invoking agent hub, plus any agent hubs reachable through a `hub_link`.

### Rule-Based Fallback
- If remote classifier fails/times out → regex rule-based classification (keywords: "code", "search", "retrieve").

### Circuit Breaker
- After N consecutive failures (default: 5) → auto-bypass classifier for M seconds (default: 60) → then retry.

---

## 2. LangGraph Multi-Agent Routing & Orchestration ✅

### Multi-Agent Workflows
Instead of basic scatter-gather, GuardRoute orchestrates complex graphs involving multiple specialized agents.
- Workflows support instantiating discrete Agent Nodes (e.g., Retrieval Agent, Coding Agent, Planner Agent).
- Agents can act sequentially or in parallel, utilizing logic blocks (IfElse, Router) to pass state dynamically.
- **V6:** the scatter-gather orchestration itself is unchanged, but **sub-agent resolution is hub-scoped**. An Agent Node stores `{ hub_id, agent_id }`, and the referenced agent hub must be linked to the workflow hub (`workflow → agent`). Resolution goes through `common/services/hub_resolver.py` at author time, on publish, and again at execution time.

### State Transfer & Execution Boundaries
- The central `GraphState` passes structured payloads between agents.
- Agents receive state, execute tool calls (e.g., SyntraFlow MCP tools), and append their structured outputs (`SubAgentResult`) back to the state.
- **Flow Terminations (V5 Strict Boundaries):** Every valid workflow MUST terminate explicitly in an `ActionNode` (executes a webhook/side-effect) or a `FinalMessageNode` (returns an LLM synthesis to the user). Dangling flows are blocked.

### Partial Failures & Circuit Breaking
- Timeout/error in an agent sets status to `ERROR`/`TIMEOUT` in Graph State, triggering fallback branches.
- No blocking from single subagent failure if a failure branch is defined.
- Configurable timeout per agent node: `AGENT_TIMEOUT_SECONDS=30`.

### Visual Graph Translation & Custom ReactFlow Canvas (V2 ✅)
- **Parser (`projects/guardroute/src/core/graph_parser.py`):** Converts visual ReactFlow JSON configurations (nodes, edges, node parameters) into compiled, executable LangGraph `StateGraph` instances.
- **Topology Safety Constraints:** Validates node linkage, ensures presence of terminal synthesis/gather nodes, and performs Kahn's algorithm cycle detection to prevent infinite execution loops.
- **Custom ReactFlow Canvas Nodes:**
  - `ClassifierNode`: Visual Arch-Router classification with confidence thresholds.
  - `AgentNode`: Visual representation of subagent with model ID, system prompt override indicator, and active tool badges. In V6 it also shows the **source agent hub** and flags a missing `hub_link`.
  - `RetrievalNode`: Visual SyntraFlow hybrid vector search with top-k limit, targeting a qualified `{ hub_id, collection_id }` reference in a linked ingestion hub.
  - `SynthesisNode`: Aggregator node compiling Scatter-Gather subagent outputs.
- **Interactive Property Drawer (`frontend/src/components/PropertyDrawer.tsx`):** Reactive parameter form updating node data, model assignments, system prompts, confidence thresholds, and hybrid search top-k parameters in real time.

---

## 3. Worker Agent Tool Connections ✅

### Python Execution Sandbox (Coding Subagent)
- `RestrictedPython` for AST-level sandboxing.
- Blocked: file I/O, subprocess, network, `__import__`, `eval`, `exec`.
- Allowed: math, strings, list comprehensions, basic data structures.
- Timeout: 10 seconds. Memory limit enforced.

### Search Engine Connectors (Web Search Subagent)
- `duckduckgo-search` library.
- Tight timeouts (max 2 seconds).
- Returns top 5 results with title, URL, snippet.

---

## 4. LLM Completion & Provider Fallbacks ✅

### Multi-Provider Routing
- Uses `completion_with_fallback()` from `common.clients.litellm`.
- Primary: `gemini/gemini-3.5-flash` (configurable via `COMPLETION_MODEL`).
- Default fallback chain (see `references/logic/model_registry.md` §6 for full list):
  1. `gemini/gemini-3.5-flash`
  2. `groq/llama-3.3-70b-versatile`
  3. `openrouter/google/gemini-3.5-flash:free`
  4. `openrouter/qwen/qwen3-235b:free`
  5. `openrouter/meta-llama/llama-4-scout:free`

### Capability-Aware Context Truncation
- Frontier models (1M+): no truncation.
- Mid-tier (70B): 32,000 tokens.
- Small free-tier: 8,000 tokens.
- Truncation in **tokens** (not characters) via `tiktoken` or LiteLLM.

---

## 5. Conversation History & Session Management ✅

### Session Storage
- Active sessions in Redis (keyed by session ID).
- Completed conversations persisted to PostgreSQL `guardroute_sessions` table.

### Multi-Turn Context
- Sliding window of conversation history (default: 20 turns).
- Context overflow → summarize older turns using the LLM.

### Session Lifecycle
- Create on first message.
- Expire after configurable timeout (default: 30 minutes).
- Support explicit session reset via API.

---

## 6. Streaming Response Support ✅

### Server-Sent Events (SSE)
- `sse-starlette` library.
- Endpoint: `POST /api/guardroute/chat` with `Accept: text/event-stream`.
- Stream LLM output token-by-token.

### Metadata Header
Before streaming tokens, emit metadata event with:
- Complexity classification result.
- Active subagent list.
- Classification latency (ms).
- Model used for synthesis.

---

## 7. Runtime Guardrails (Pre/Post LLM) ✅
- **Pre-Flight:** Prompt injection detection, system prompt override blocking.
- **Post-Flight:** PII scanning, optional toxicity check (threshold: 0.1).
- See `references/logic/security.md` §3 for details.

---

## 8. Async Session Logging & Auditing ✅

### Execution Spans Tracing
Trace payload includes: user prompt, complexity categorization, subagent list with latencies, total latency, response text, model used per step, token counts. V6 adds `hub_id` (and, for workflow runs, `workflow_id` / `run_id`) to every span so traces can be filtered per hub.

### Kafka Writes
- Publish to `guardroute-traces` topic.
- Kafka offline → fallback to local file logging.

### Token Usage Tracking
- Cumulative input/output tokens per session.
- Stored in `guardroute_usage` PostgreSQL table.

---

## 9. Dependencies (`pyproject.toml` guardroute extras)
```toml
guardroute = [
    "langchain (>=0.3.0,<1.0.0)",
    "langgraph (>=0.2.0,<1.0.0)",
    "mcp (>=0.1.0,<1.0.0)",
    "confluent-kafka (>=2.4.0,<3.0.0)",
    "duckduckgo-search (>=6.0.0,<7.0.0)",
    "RestrictedPython (>=7.0,<8.0)",
    "sse-starlette (>=2.0.0,<3.0.0)",
    "tiktoken (>=0.7.0,<1.0.0)",
]
```

---

## 10. V6 Route Surface

Agent routes are guarded by `require_hub(hub_type='agent', ...)` and workflow routes by
`require_hub(hub_type='workflow', ...)`. The flat `/agents` and `/workflows` route trees are
**removed** — no back-compat aliases are retained.

| Method | Path | Min role | Purpose |
|---|---|---|---|
| `GET` / `POST` | `/hubs/{hub_id}/agents` | viewer / contributor | List, create agents (`UNIQUE (hub_id, endpoint_slug)`) |
| `GET` / `PATCH` | `/hubs/{hub_id}/agents/{agent_id}` | viewer / contributor | Inspect, update definition and collection bindings |
| `DELETE` | `/hubs/{hub_id}/agents/{agent_id}` | maintainer | Delete agent |
| `POST` | `/hubs/{hub_id}/agents/{agent_id}/invoke` | contributor | Invoke (SSE streaming supported) |
| `GET` | `/hubs/{hub_id}/agents/{agent_id}/invocations` | viewer | Hub-scoped invocation log |
| `GET` / `POST` | `/hubs/{hub_id}/workflows` | viewer / contributor | List, create workflows |
| `GET` / `PATCH` | `/hubs/{hub_id}/workflows/{wf_id}` | viewer / contributor | Inspect, update metadata |
| `PUT` | `/hubs/{hub_id}/workflows/{wf_id}/draft` | contributor | Save draft graph (optimistic `If-Match`) |
| `POST` | `/hubs/{hub_id}/workflows/{wf_id}/publish` | contributor | Freeze draft into an immutable version |
| `POST` | `/hubs/{hub_id}/workflows/{wf_id}/run` | contributor | Execute the published version (SSE) |
| `GET` | `/hubs/{hub_id}/workflows/{wf_id}/runs` | viewer | Persisted run history |

The full workflow route table, including versions, restore, duplicate and import/export, lives in
[`workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) §3.
