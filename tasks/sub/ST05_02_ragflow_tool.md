# ST05_02: Implement LangChain @tool for RAGFlow Retrieval

## Parent Link
`base/BT05_rag_integration.md`

## Actionable Steps
- [x] Step 1: Create `ragflow_retrieve` @tool with proper docstring
- [x] Step 2: Call `get_ragflow_client().retrieve()` via `asyncio.to_thread()`
- [x] Step 3: Format output with document name and relevance score
- [x] Step 4: Register in orchestrator's `get_agent_tools()` base tool set
- [x] Step 5: Handle RAGFlow unavailability gracefully (returns error string)

## Dependencies
- `ST05_01_ragflow_client.md` — RAGFlowClient singleton ✅
- RAGFlow container must be running at RAGFLOW_API_URL ✅ (validated separately)

## Implementation Notes
- `asyncio.to_thread()` wraps synchronous SDK call for async safety
- Hard cap: `top_k` max 10
- Returns descriptive fallback if RAGFlow is unavailable or dataset empty
- Tool available to ALL roles (standard, premium, admin) — same as vector_search

## Completion
**Status: [x] COMPLETE** — Implemented 2026-07-14 (as part of BT04 tooling).
`../../echomind-core/app/agent/tools/ragflow_retrieval.py`
