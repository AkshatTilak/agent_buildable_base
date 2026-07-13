# ST04_02: Implement Vector Search Tool

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [ ] Step 1: Implement embedding generation for search queries
- [ ] Step 2: Implement pgvector cosine similarity query with user_id filtering
- [ ] Step 3: Format search results with chunk text and similarity scores
- [ ] Step 4: Register as LangChain @tool with descriptive docstring
- [ ] Step 5: Test with sample embeddings in vector_knowledge table

## Dependencies
- `ST02_01_init_db_sql.md` — vector_knowledge table with HNSW index
- `pgvector` Python package

## Definition of Done
Tool returns semantically relevant document chunks filtered by user, similarity scores accurate.
