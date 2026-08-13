# Subtask 05_03: Collections Real Integration Tests

## Parent Base Task
`tasks/v8/base/05_syntraflow_ingestion_retrieval_real.md`

## Objective
Create `tests/integration/syntraflow/test_collections_real.py` — collection management & schema sync.

## Tasks
1. [x] Create/list/delete collections on real Qdrant.
2. [x] Collection schema sync between Postgres and Qdrant.
3. [x] Collection access control via hub bindings.

## Definition of Done
- Collection create/list/delete verified against real Qdrant.
- Schema sync between Postgres and Qdrant verified.
- Collection access control via hub bindings verified.

## Status
- `test_collections_real.py` created; **3 tests pass** against real Postgres & Qdrant.
- CRUD lifecycle, Qdrant physical schema sync (768-dim), and hub isolation verified.
