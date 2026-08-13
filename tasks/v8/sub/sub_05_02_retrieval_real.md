# Subtask 05_02: Retrieval Real Integration Tests

## Parent Base Task
`tasks/v8/base/05_syntraflow_ingestion_retrieval_real.md`

## Objective
Create `tests/integration/syntraflow/test_retrieval_real.py` — retrieval engine, hybrid, metadata filtering, hub scoping.

## Tasks
1. [ ] Ingest known documents → query with known terms → verify relevant chunks returned.
2. [ ] Vector-only retrieval vs hybrid (vector + graph) retrieval comparison.
3. [ ] Metadata filtering (by source, date, collection).
4. [ ] Hub-scoped retrieval (only returns docs from bound collections).
5. [ ] Test retrieval with both `jina-clip-v2` (1024-dim) and `harrier-0.6b` (768-dim) collections.

## Definition of Done
- Retrieval returns relevant chunks with metadata filtering and hub scoping.
- Vector-only vs hybrid retrieval compared.
- Retrieval verified with both 1024-dim and 768-dim collections.
