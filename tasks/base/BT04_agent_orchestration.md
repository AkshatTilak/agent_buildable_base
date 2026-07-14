# BT04: LangChain Agent Orchestration

## Objective
Implement the core LangChain agent with multi-tool routing, conversational context management, and role-aware tool access.

## Business/System Value
This is the reasoning core of System 1 — the agent that routes queries, invokes tools, and generates intelligent responses.

## Subtask Registry
- [x] `sub/ST04_01_orchestrator.md` — Implement the main agent orchestrator with Gemini LLM
- [x] `sub/ST04_02_vector_search_tool.md` — Implement pgvector cosine similarity search tool
- [x] `sub/ST04_03_analytics_tool.md` — Implement user_analytics query tool
- [x] `sub/ST04_04_chat_history.md` — Implement Redis-backed chat history integration
- [x] `sub/ST04_05_system_prompt.md` — Finalize and iterate on system prompt engineering

## Complexity Rating
**High** — Core AI logic; requires careful tool orchestration and prompt engineering.

## Source Code Location
`../../echomind-core/app/agent/`

## Completion
**Status: [x] COMPLETE** — Implemented 2026-07-14.

Key design decisions:
- LLM: `gemini-2.0-flash` via `ChatGoogleGenerativeAI` (LangChain 0.3 API)
- Agent: `create_tool_calling_agent()` + `AgentExecutor` with `return_intermediate_steps=True`
- RBAC: `get_agent_tools(role)` filters tool list at executor creation time (standard/premium/admin)
- Observability: Langfuse `CallbackHandler` per request (not decorator pattern) for clean trace grouping
- Chat history: `RedisChatMessageHistory` loaded pre-invoke, appended post-invoke
- Sources: Extracted from intermediate steps by parsing tool output strings

> ⚠️ Prompt iteration (ST04_05 Step 5) deferred to post-BT04 sprint after Langfuse traces are available.
> ⚠️ BT05 partially completed: `ragflow_client.py` and `ragflow_retrieval.py` tool implemented as part of BT04 tool set. Remaining BT05 work: ST05_03 (Docker validation).
