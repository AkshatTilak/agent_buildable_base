# ST04_02: Implement Vector Search Tool

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [x] Step 1: Implement embedding generation for search queries
- [x] Step 2: Implement pgvector cosine similarity query with user_id filtering
- [x] Step 3: Format search results with chunk text and similarity scores
- [x] Step 4: Register as LangChain @tool with descriptive docstring
- [x] Step 5: Test with sample embeddings in vector_knowledge table

## Dependencies
- `ST02_01_init_db_sql.md` — vector_knowledge table with HNSW index ✅
- `pgvector` Python package ✅
- `langchain-google-genai` for GoogleGenerativeAIEmbeddings ✅

## Implementation Notes
- Embedding model: `models/text-embedding-004` with `task_type="RETRIEVAL_QUERY"`
- Output dimensionality: 3072 (matches `VECTOR(3072)` schema column)
- Query: `<=>` cosine distance operator, ordered ASC, `1 - distance` = similarity score
- JOIN with `documents` table for user_id filtering and status='COMPLETE' guard
- Uses `async_session()` from `app.db.session`
- Hard cap: `top_k` max 20 to prevent excessive DB load

## Definition of Done
Tool returns semantically relevant document chunks filtered by user, similarity scores accurate. ✅

## Completion
**Status: [x] COMPLETE** — Implemented 2026-07-14.
`../../echomind-core/app/agent/tools/vector_search.py`
