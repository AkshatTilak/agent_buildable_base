# Subtask: Graceful Degradation

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Neo4j unavailable: disable GraphRAG features; log warning; vector-only retrieval.
- [x] Kafka unavailable: fall back to local file logging for traces.
- [x] Inference Server unavailable: return `503 Service Unavailable` for model endpoints; cloud-only models still work.
- [x] Redis unavailable: disable caching and rate limiting; log warning.

## Dependencies
- All service clients with health check capability.

## Definition of Done
- Platform remains functional with any single optional service down. Warnings logged.
