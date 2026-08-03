# Subtask 03_02: Infrastructure UI & Dependency Management View

## Tasks
1. Overhaul `frontend/src/components/InfrastructurePage.tsx`.
2. Add dynamic health status indicators for core databases (PostgreSQL, Redis, Neo4j, Qdrant, Kafka, Inference).
3. For non-core services (e.g. pgAdmin, Redis Insight, Kafka UI), render actionable service cards displaying:
   - Current reachability status.
   - Redirect / launch links if active.
   - Exact CLI / Docker commands to spin them up locally if offline.
