# BT04: LangChain Agent Orchestration

## Objective
Implement the core LangChain agent with multi-tool routing, conversational context management, and role-aware tool access.

## Business/System Value
This is the reasoning core of System 1 — the agent that routes queries, invokes tools, and generates intelligent responses.

## Subtask Registry
- [ ] `sub/ST04_01_orchestrator.md` — Implement the main agent orchestrator with Gemini LLM
- [ ] `sub/ST04_02_vector_search_tool.md` — Implement pgvector cosine similarity search tool
- [ ] `sub/ST04_03_analytics_tool.md` — Implement user_analytics query tool
- [ ] `sub/ST04_04_chat_history.md` — Implement Redis-backed chat history integration
- [ ] `sub/ST04_05_system_prompt.md` — Finalize and iterate on system prompt engineering

## Complexity Rating
**High** — Core AI logic; requires careful tool orchestration and prompt engineering.

## Source Code Location
`../../echomind-core/app/agent/`
