# Subtask: ST-07 — Hybrid Search Subsystem Implementation

**Parent Link:** [BT-03: Hybrid Search Engine & AI Agent Subsystem](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-03_search_ai_engine.md)

## Objective
Build `services/search-service/` implementing a 3-way hybrid search algorithm combining PostGIS spatial queries, lexical BM25 text search (OpenSearch / PostgreSQL BM25), and `pgvector` dense vector embeddings merged via Reciprocal Rank Fusion (RRF).

---

## Technical Actionable Steps

- [ ] **Step 1: Implement Spatial Query Pipeline (PostGIS)**
  - Write PostGIS queries for radius searches (`ST_DWithin`), bounding box (`ST_MakeEnvelope`), and drive-time spatial filters.

- [ ] **Step 2: Implement Lexical Keyword Search (BM25)**
  - Configure OpenSearch index / PostgreSQL full-text search (`tsvector`) for facility names, medical specialties, city names, and licenses.

- [ ] **Step 3: Implement Dense Vector Embedding Generator & Search (`pgvector`)**
  - Integrate embedding generator (`text-embedding-3-small` / local HuggingFace model).
  - Store vector embeddings in `facilities.embedding` (`vector(1536)`).
  - Write k-NN vector distance queries (`<->` cosine distance).

- [ ] **Step 4: Build Reciprocal Rank Fusion (RRF) Scorer API**
  - Combine ranked results from Spatial, BM25, and Vector pipelines using RRF scoring formula:
    $$RRF(d) = \sum_{m \in M} \frac{1}{k + r_m(d)}$$
  - Expose `/search` endpoint supporting filters (care type, pricing max, amenities, location point, natural language query string).

---

## Dependencies
- Completion of `ST-01_monorepo_and_db_scaffold.md` and `ST-03_taxonomy_and_common_packages.md`.

## Complexity Rating
**High**

## Definition of Done
- `/search` endpoint returns ranked facility results combining geo-proximity, keyword match, and semantic query intent.
- Benchmark tests show sub-200ms latency for hybrid search execution.
