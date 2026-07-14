# ST04_01: Implement Agent Orchestrator

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [x] Step 1: Initialize ChatGoogleGenerativeAI with Gemini model via LangChain
- [x] Step 2: Implement `get_agent_tools()` with role-based tool filtering
- [x] Step 3: Create agent using `create_tool_calling_agent()` with system prompt
- [x] Step 4: Wrap in AgentExecutor with verbose mode and Langfuse callback
- [x] Step 5: Add Langfuse callback handler for per-request trace capture
- [x] Step 6: Wire into FastAPI chat endpoint (POST /api/v1/chat)

## Dependencies
- `langchain`, `langchain-google-genai` installed ✅
- GOOGLE_API_KEY set in environment (required at runtime)

## Implementation Notes
- Model: `gemini-2.0-flash` via `ChatGoogleGenerativeAI`
- `return_intermediate_steps=True` on AgentExecutor for source extraction
- `handle_parsing_errors=True` for resilience
- `max_iterations=10` to prevent infinite loops
- Langfuse integrated via `CallbackHandler` per-request (not @observe decorator)
- Chat history appended post-response (HumanMessage + AIMessage)
- Source extraction via `_extract_sources()` parsing tool outputs

## Definition of Done
Agent responds to queries, routes to tools based on role, execution is traced in Langfuse. ✅

## Completion
**Status: [x] COMPLETE** — Implemented 2026-07-14.
`../../echomind-core/app/agent/orchestrator.py`
