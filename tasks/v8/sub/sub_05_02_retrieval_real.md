# Subtask 05_02: Retrieval Real Integration Tests

## Parent Base Task
`tasks/v8/base/05_syntraflow_ingestion_retrieval_real.md`

## Objective
Create `tests/integration/syntraflow/test_retrieval_real.py` — retrieval engine, hybrid, metadata filtering, hub scoping.

## Tasks
1. [x] Ingest known documents → query with known terms → verify relevant chunks returned.
2. [x] Vector-only retrieval vs hybrid (vector + graph) retrieval comparison.
3. [x] Metadata filtering (by source, date, collection).
4. [x] Hub-scoped retrieval (only returns docs from bound collections).
5. [x] Test retrieval with both `jina-clip-v2` (1,024-dim) and `harrier-0.6b` -> `microsoft/harrier-oss-v1-0.6b` (1,024-dim) collections; use Harrier 270M separately for a 640-dim collection.

## Definition of Done
- Retrieval returns relevant chunks with metadata filtering and hub scoping against actual running Postgres, Qdrant (`:6333`), and Neo4j (`:7687`).
- Vector-only vs hybrid retrieval compared.
- Retrieval verified with the two 1,024-dim models and separately with the 640-dim Harrier 270M contract.
- If errors arise, inspect Docker container logs (`docker compose logs qdrant neo4j db`) and resolve root causes in backend retrieval code.
- Deleting test data is not necessary.

## Status
- `test_retrieval_real.py` created; **5 tests pass** against actual running Postgres, Qdrant & Neo4j.
- Fixed `search_hub` to surface `document_id` from hit metadata and to re-raise `HTTPException` (404) for cross-hub collection access instead of 500.
