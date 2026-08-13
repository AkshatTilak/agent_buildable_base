# Goal: Version 8 — Real-World Test Suite & System Optimization

## Objective
Replace the current mock-heavy, in-memory-only test suite with a layered testing infrastructure that hits **real services** (Postgres, Qdrant, Redis, Neo4j, Kafka, LiteLLM), validates full frontend-to-backend data flows, tests all WebSocket/SSE streaming interactions, and exposes real-world bugs. Simultaneously optimize the backend and frontend for performance, deduplication, and caching.

## Key Outcomes

1. **Test Infrastructure & Configuration Foundation**:
   - `.env.test` (gitignored) + `.env.test.example` (committed) with real service credentials.
   - Root `conftest.py` with `unit` / `integration` / `e2e` / `live_api` / `streaming` / `performance` markers and real-service fixtures (`real_db_session`, `qdrant_client`, `redis_client`, `neo4j_driver`, `gateway_client`, seed factories).
   - `docker-compose.test.yml` standing up Postgres, Qdrant, Redis, Neo4j, Kafka + Zookeeper with ephemeral volumes, once per session.
   - Gateway (`:8100`) and Inference (`:8110`) started as `poetry run uvicorn` subprocesses via session-scoped fixtures.
   - Structured test logging (JSON reporter, HTTP/DB/streaming capture) and reorganized `tests/` directory hierarchy.

2. **Existing Test Reorganization**:
   - All 77 existing test files audited, classified, and migrated into `tests/unit/`, `tests/integration/gateway/`, etc. without breaking currently-passing tests.
   - Submodule tests consolidated into the root `tests/` hierarchy; every file tagged with the correct marker.

3. **Real-World Integration Tests**:
   - **Auth & User Lifecycle** — registration, login, JWT, password reset, invites, RBAC, lockout, OAuth linking against real Postgres.
   - **Hub Management & Linking** — hub CRUD, membership, hub links, datastore bindings, cross-hub access.
   - **SyntraFlow** — ingestion & retrieval against real Qdrant/Neo4j, local Harrier OSS v1 (`microsoft/harrier-oss-v1-0.6b` at 1,024 dimensions and `microsoft/harrier-oss-v1-270m` at 640 dimensions), `jinaai/jina-clip-v2` at 1,024 dimensions, and API (`gemini/gemini-embedding-2`) embedder paths, plus local OCR.
   - **GuardRoute** — agent CRUD, workflow lifecycle/versioning, workflow execution, node executors against real backends.
   - **EvalOps** — dataset management, DeepEval/RAGAS runner execution, eval hub management.

4. **End-to-End Workflow Tests**:
   - Full user journeys (signup → hub → agent/workflow/ingestion → eval → cleanup).
   - Multi-user collaboration, hub-linking cross-access, workflow versioning/portability.

5. **Frontend-Backend Contract Tests**:
   - `tests/e2e/contracts/test_api_contracts.py` validating `api.ts` calls against real gateway route schemas.
   - `scripts/extract_api_types.py` parsing TS interfaces into JSON Schema; coverage gap report script.

6. **Live API Tests**:
   - LiteLLM routing & fallback chains, inference proxy, real MCP tool integration, Kafka messaging with offline fallback.

7. **Performance Optimizations**:
   - **Backend** — Redis caching layer (hub, session, model registry, collection metadata), query deduplication (N+1 fixes), DB indexes, cursor pagination, lazy route loading, cache invalidation.
   - **Frontend** — React Query deduplication/staleTime, Zustand state cleanup, component memoization, virtual scrolling, lazy loading, bundle tree-shaking, request batching.

8. **Performance Benchmark Tests**:
   - Response-time assertions, caching effectiveness, concurrent-access isolation.

9. **WebSocket & SSE Streaming Tests**:
   - Telemetry WS/SSE, workflow run SSE, agent invoke SSE, playground SSE, external API SSE — end-to-end against a running gateway with real Redis pub/sub.

10. **MCP Tools Ecosystem**:
    - User-facing `mcp_tools/` directory with sample tool servers (calculator, web search, code executor) + `_template`.
    - Full MCP lifecycle tests and MCP-node-in-workflow tests.

11. **Comprehensive API Surface Coverage**:
    - Every gateway router endpoint has at least one real integration test; coverage gap audit script.

12. **Test Observability & Logging**:
    - Structured JSON logs per test, `X-Test-Trace-ID` request tracing, failure diagnostics, `pytest-html` report, JUnit XML + JSON CI output.

## Acceptance Criteria
All 17 Base Task groups and their Subtasks marked `[x]`, with:
- All 77 existing tests still passing after reorganization.
- Docker test services start/stop cleanly with ephemeral volumes.
- Real-service integration, streaming, E2E, live API, and performance tests passing.
- Frontend optimizations validated via Lighthouse / DevTools before/after metrics.
- Redis cache hit/miss ratios reviewed in gateway logs.
- WebSocket/SSE streams verified stable in browser DevTools.
