# ST04_01: Implement Agent Orchestrator

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [ ] Step 1: Initialize ChatGoogleGenerativeAI with Gemini model via LangChain
- [ ] Step 2: Implement `get_agent_tools()` with role-based tool filtering
- [ ] Step 3: Create agent using `create_tool_calling_agent()` with system prompt
- [ ] Step 4: Wrap in AgentExecutor with verbose mode
- [ ] Step 5: Add @observe decorator for Langfuse tracing
- [ ] Step 6: Wire into a FastAPI chat endpoint (POST /api/v1/chat)

## Dependencies
- `langchain`, `langchain-google-genai` installed
- GOOGLE_API_KEY set in environment

## Definition of Done
Agent responds to queries, routes to tools, and execution is traced in Langfuse.
