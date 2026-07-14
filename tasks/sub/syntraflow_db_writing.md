# Subtask: SyntraFlow Database Writing & KG Population

**Parent Link:** `base/syntraflow_rag.md`

## Actionable Steps
- [x] Write metadata, segments, and chunk payloads to Postgres (`syntraflow_` prefix).
- [x] Vector Indexing: Generate embeddings (default: jina-clip-v2), index in Qdrant collection `syntraflow_chunks_v1`. Batch upserts (100 vectors).
- [x] KG Population: Run LLM entity/relationship extraction over chunks. Write nodes/edges to Neo4j (`SyntraFlow_` prefix).

## Definition of Done
- Ingestion fully populates PostgreSQL, Qdrant vectors, and Neo4j entities/relationships.
