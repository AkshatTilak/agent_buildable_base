# Subtask: Architecture Common Library Refactor

**Parent Link:** `base/06_architecture_cleanup.md`

## Actionable Steps
- [ ] Review all DB connections (Postgres, Qdrant, Neo4j, Redis, Kafka) across `gateway`, `inference`, `projects/syntraflow`, and `projects/guardroute`.
- [ ] Consolidate duplicate initialization and connection-pool settings into `common/clients/`.
- [ ] Ensure all microservices use the unified client factory functions from the `common` package.
- [ ] Standardize the configuration initialization logic using the common Settings model, loading configurations from a single centralized `.env` file structure.

## Dependencies
- None

## Definition of Done
- Duplicate database setup logic is deleted from separate microservices.
- All services initialize DB connections through imports from `common.clients`.
