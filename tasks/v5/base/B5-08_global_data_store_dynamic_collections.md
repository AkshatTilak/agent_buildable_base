# B5-08: Global Data Store & Dynamic Collections

> **Status:** `[x]`  
> **Owner:** `projects/syntraflow`  
> **Secondary:** `common/clients`, `frontend`  
> **Complexity:** 🔴 High (5 subtasks)

---

## Objective

Transition SyntraFlow to a multi-collection Qdrant architecture with dynamic metadata filtering, isolated knowledge bases, and pluggable retrieval strategies (Dense, Sparse/BM25, Hybrid RRF, Neo4j Graph).

---

## Acceptance Criteria

- [x] Relational schema updated with `syntraflow_collections` SQL table and Alembic migration
- [x] API endpoints available for collection CRUD (`/api/syntraflow/collections`)
- [x] Vector indexing automatically tags metadata (`tenant_id`, `document_id`, `tags`, `access_level`)
- [x] Retrieval strategy engine allows selecting Dense, Sparse, Hybrid (RRF), or Graph search per query
- [x] Frontend SyntraFlow view features a Collection Manager tab and interactive retrieval tester

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-08a | DB Schema Migration & Collections Table | [`S5-08a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-08a.md) |
| S5-08b | Qdrant Dynamic Collection Manager & API | [`S5-08b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-08b.md) |
| S5-08c | Dynamic Metadata Indexing & Filtering Pipeline | [`S5-08c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-08c.md) |
| S5-08d | Pluggable Retrieval Strategy Engine | [`S5-08d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-08d.md) |
| S5-08e | Frontend: Collection Manager & Query Panel | [`S5-08e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-08e.md) |
