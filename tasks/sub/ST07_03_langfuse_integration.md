# ST07_03: Wire Langfuse into Agent & Tools

## Parent Link
`base/BT07_observability.md`

## Actionable Steps
- [ ] Step 1: Add @observe() decorator to `run_agent()` in orchestrator
- [ ] Step 2: Add @observe() decorator to all LangChain @tool functions
- [ ] Step 3: Inject user_id and tags into trace context via `langfuse_context.update_current_trace()`
- [ ] Step 4: Verify hierarchical traces (agent → tool calls) appear in Langfuse UI
- [ ] Step 5: Validate token usage and latency metrics are captured

## Dependencies
- `ST07_02_langfuse_sdk.md` — Langfuse client initialized
- `ST04_01_orchestrator.md` — Agent orchestrator implemented

## Definition of Done
All agent executions produce complete, hierarchical traces in Langfuse with user context.
