# ST04_05: System Prompt Engineering

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [x] Step 1: Define system prompt with role descriptions and behavioral guidelines
- [x] Step 2: Include tool usage instructions in the prompt
- [x] Step 3: Add citation and source reference formatting rules
- [x] Step 4: Implement as ChatPromptTemplate (required by create_tool_calling_agent)
- [ ] Step 5: Iterate based on Langfuse trace analysis (ongoing — post-BT04)

## Dependencies
- Agent orchestrator and tools must be functional for testing ✅

## Implementation Notes
- Prompt is a `ChatPromptTemplate.from_messages([...])` — NOT a plain string
- Structure: `("system", ...)`, `MessagesPlaceholder("chat_history")`, `("human", "{input}")`, `MessagesPlaceholder("agent_scratchpad")`
- This structure is contractually required by `create_tool_calling_agent`
- Includes per-tool guidance: when to use vector_search vs ragflow_retrieve
- Includes citation format: `[Source: <document name>, chunk <index>]`
- Iteration (Step 5) deferred — will be done post-BT04 via Langfuse trace review

## Definition of Done
System prompt produces coherent, tool-aware responses across diverse query types. ✅

## Completion
**Status: [x] COMPLETE** — Implemented 2026-07-14. (Iteration step deferred to post-BT04 sprint)
`../../echomind-core/app/agent/prompts/system_prompt.py`
