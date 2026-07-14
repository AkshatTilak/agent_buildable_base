# Subtask: SyntraFlow Cascade Document Deletion

**Parent Link:** `base/syntraflow_rag.md`

## Actionable Steps
- [x] Implement `DELETE /api/syntraflow/documents/{doc_id}` cascade delete.
- [x] Remove vectors from Qdrant by document ID filter.
- [x] Remove graph nodes/edges from Neo4j.
- [x] Remove rows from Postgres tables (`syntraflow_documents`, `syntraflow_chunks`, `syntraflow_video_segments`, `syntraflow_jobs`).

## Definition of Done
- Deletion cascade cleanly removes document references from all SQL, vector, and graph storage slots.
