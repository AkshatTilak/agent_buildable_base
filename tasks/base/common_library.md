# Base Task: Shared Library (common/)

## Objective
Implement a shared library containing unified configuration parsing, database clients (Postgres, Qdrant, Neo4j, Redis), OpenTelemetry observability tracing/logging, and core schemas.

## Business/System Value
Enforces DRY (Don't Repeat Yourself) principle and provides standardized type safety and database connection pools for all project submodules (Gateway, Inference, SyntraFlow, GuardRoute, EvalOps).

## Subtask Registry
- [x] `sub/common_config_composition.md` — Pydantic-Settings composition pattern
- [x] `sub/common_config_validation.md` — Validation on startup for environment variables
- [x] `sub/common_postgres_client.md` — Asynchronous PostgreSQL client with AsyncSession
- [x] `sub/common_qdrant_client.md` — Async vector similarity search client
- [x] `sub/common_neo4j_client.md` — Neo4j client with read-only Cypher enforcement
- [x] `sub/common_inference_client.md` — Async Inference client with timeouts and retries
- [x] `sub/common_litellm_client.md` — Multi-provider client wrapper with fallback capabilities
- [x] `sub/common_redis_client.md` — Async Redis client wrapper
- [x] `sub/common_observability_logger.md` — Structured JSON logging
- [x] `sub/common_observability_tracing.md` — OpenTelemetry span tracing
- [x] `sub/common_schemas.md` — Pydantic models for agent states, model specs, and standard APIs

## Complexity Rating
**Medium**

## References
- `references/code/common_library.md` — Code details.
- `references/structure/system_architecture.md` — Connections specs.
