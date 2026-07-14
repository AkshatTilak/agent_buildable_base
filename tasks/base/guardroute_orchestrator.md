# Base Task: GuardRoute Decision Routing & Orchestration

## Objective
Implement intelligent query complexity classification, LangGraph coordinate orchestration, subagent integrations (Retrieval, Coding, Web Search), fallback LLM chains, session storage, and SSE streaming.

## Business/System Value
Acts as the central coordination brain of the user interaction layer. Decides how to execute queries, calls specialist agents in parallel, and merges contexts into streaming output responses.

## Subtask Registry
- [x] `sub/guardroute_intent_classifier.md` — Complexity categorizer and model routing
- [x] `sub/guardroute_langgraph_orchestrator.md` — Scatter-gather graph layout
- [x] `sub/guardroute_python_sandbox.md` — RestrictedPython execution sandbox
- [x] `sub/guardroute_search_connector.md` — DuckDuckGo search subagent
- [x] `sub/guardroute_llm_fallbacks.md` — LiteLLM multi-provider fallback chain
- [x] `sub/guardroute_context_truncation.md` — Capability-aware context truncation
- [x] `sub/guardroute_session_management.md` — Redis/Postgres conversation caching and serialization
- [x] `sub/guardroute_sse_streaming.md` — Starlette SSE streaming with metadata headers
- [x] `sub/guardroute_runtime_guardrails.md` — Pre-flight injection checks and post-flight PII redaction
- [x] `sub/guardroute_async_logging.md` — Kafka execution logs and span publishing

## Complexity Rating
**High**

## References
- `references/logic/guardroute.md` — Orchestrator specs.
- `references/logic/security.md` — Sandbox and guardrail specs.
