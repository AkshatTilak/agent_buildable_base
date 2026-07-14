# Agent Orchestration — Code Reference

**Created:** 2026-07-14 | **BT04 COMPLETE**

## File Map

| File | Purpose |
|---|---|
| `app/agent/orchestrator.py` | Main `run_agent()` function + `get_agent_tools()` |
| `app/agent/prompts/system_prompt.py` | `SYSTEM_PROMPT` ChatPromptTemplate |
| `app/agent/tools/vector_search.py` | `vector_similarity_search` @tool |
| `app/agent/tools/user_analytics.py` | `query_user_analytics` @tool |
| `app/agent/tools/ragflow_retrieval.py` | `ragflow_retrieve` @tool |
| `app/rag/ragflow_client.py` | `RAGFlowClient` singleton |
| `app/cache/chat_history.py` | `get_redis_chat_history()` |
| `app/observability/tracing.py` | `init_langfuse()`, `get_langfuse_callback()`, `flush_langfuse()` |
| `app/api/routes/chat.py` | `POST /api/v1/chat` — wired to `run_agent()` |

## Key Design Decisions

### 1. LangChain Version
Using LangChain `^0.3.0` (modern API). Use `langchain.agents.create_tool_calling_agent` and `AgentExecutor` — NOT the deprecated `initialize_agent()` or `AgentType` enum.

### 2. Embedding Model vs. Schema Alignment
- Schema: `VECTOR(3072)` (comment says "text-embedding-3-large" but we use Google)
- Implementation: `GoogleGenerativeAIEmbeddings(model="models/text-embedding-004")`
- The `text-embedding-004` model supports output dimensions up to 3072.
- In `vector_search.py`, embeddings are generated without explicit `output_dimensionality` parameter (defaults to model max). The pgvector query uses string casting: `CAST(:query_vec AS vector)`.

> ⚠️ **If dimension mismatch occurs**: Add `output_dimensionality=768` parameter to `GoogleGenerativeAIEmbeddings` and run a migration to change `VECTOR(3072)` to `VECTOR(768)`.

### 3. Langfuse Integration
- Pattern: `CallbackHandler` per request (NOT module-level `@observe()` decorator)
- Reason: `CallbackHandler` provides richer per-request metadata (user_id, session_id, trace_name) and works natively with `AgentExecutor.callbacks`
- The `observe` + `langfuse_context` are still re-exported from `tracing.py` for future use in other modules

### 4. ragflow_sdk Async Safety
- `ragflow_sdk` is a synchronous library (no `asyncio` support)
- All SDK calls go through `asyncio.to_thread()` in the `ragflow_retrieve` tool
- This prevents blocking the FastAPI event loop during document retrieval

### 5. Chat History Rolling Window
- `RedisChatMessageHistory` does NOT automatically enforce a window — it stores all messages
- To enforce the "last 20 messages" policy: pass only the last 20 messages to the agent:
  ```python
  # Future optimization in orchestrator.py:
  chat_history_messages = chat_history_store.messages[-20:]
  ```
- Currently passes all messages (safe for short sessions, revisit for long-running users)

## Environment Variables Required (BT04)

| Variable | Purpose | Default |
|---|---|---|
| `GOOGLE_API_KEY` | Gemini LLM + Embeddings | (required) |
| `LANGFUSE_SECRET_KEY` | Langfuse tracing auth | (optional, startup warns if missing) |
| `LANGFUSE_PUBLIC_KEY` | Langfuse tracing auth | (optional) |
| `LANGFUSE_BASE_URL` | Langfuse server URL | `http://localhost:3000` |
| `RAGFLOW_API_URL` | RAGFlow API endpoint | `http://localhost:9380` |
| `RAGFLOW_API_KEY` | RAGFlow auth key | (required for BT05 Docker validation) |

## Testing the Orchestrator

```bash
# 1. Start required services
docker compose up -d postgres redis

# 2. Install deps
poetry install

# 3. Copy & configure env
cp .env.example .env
# Set GOOGLE_API_KEY in .env

# 4. Start server
poetry run uvicorn app.main:app --reload --port 8000

# 5. Register + login
curl -X POST http://localhost:8000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "password123"}'

curl -X POST http://localhost:8000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "password123"}'
# → Copy the access_token

# 6. Chat with agent
curl -X POST http://localhost:8000/api/v1/chat \
  -H "Authorization: Bearer <access_token>" \
  -H "Content-Type: application/json" \
  -d '{"query": "What documents have I uploaded?", "session_id": "test-session-1"}'
```
