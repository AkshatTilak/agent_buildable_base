# Goal: Version 8 — Real-World Test Suite & System Optimization

## Objective
Replace the current mock-heavy, in-memory-only test suite with a layered testing infrastructure that hits **actual running services & standard ports** (Postgres `:5432`, Qdrant `:6333`, Redis `:6379`, Neo4j `:7687`, Kafka `:9092`, Gateway `:8000`, Inference `:8010`), validates full frontend-to-backend data flows, tests all WebSocket/SSE streaming interactions, and diagnoses and fixes real-world system flaws across the stack. Simultaneously optimize the backend and frontend for performance, deduplication, and caching.

## Key Outcomes

1. **Test Infrastructure & Configuration Foundation**:
   - `.env.test` (gitignored) + `.env.test.example` (committed) targeting actual running dev infrastructure & ports (`:8000` Gateway, `:8010` Inference, standard DB ports).
   - Root `conftest.py` with `unit` / `integration` / `e2e` / `live_api` / `streaming` / `performance` markers and real-service fixtures (`real_db_session`, `qdrant_client`, `redis_client`, `neo4j_driver`, `gateway_client`, seed factories) connected to actual services.
   - Subprocess/client fixtures for Gateway (`:8000`) and Inference (`:8010`) without separate ports or duplicate container stacks.
   - Structured test logging (JSON reporter, HTTP/DB/streaming capture) and reorganized `tests/` directory hierarchy.
   - No mandatory test data deletion; test entity isolation achieved via unique UUIDs and prefixed namespaces.

2. **Stack-Wide Defect Remediation**:
   - When tests encounter failures or errors, inspect **Docker container logs** (`docker compose logs <service>`) and actively **fix the root-cause bugs across the backend, frontend, gateway, inference, and submodules** (SyntraFlow, GuardRoute, EvalOps, Common Library).

3. **Existing Test Reorganization**:
   - All 77 existing test files audited, classified, and migrated into `tests/unit/`, `tests/integration/gateway/`, etc. without breaking currently-passing tests.
   - Submodule tests consolidated into the root `tests/` hierarchy; every file tagged with the correct marker.

4. **Real-World Integration Tests**:
   - **Auth & User Lifecycle** — registration, login, JWT, password reset, invites, RBAC, lockout, OAuth linking against real Postgres.
   - **Hub Management & Linking** — hub CRUD, membership, hub links, datastore bindings, cross-hub access.
   - **SyntraFlow** — ingestion & retrieval against real Qdrant/Neo4j, local Harrier OSS v1 (`microsoft/harrier-oss-v1-0.6b` at 1,024 dimensions and `microsoft/harrier-oss-v1-270m` at 640 dimensions), `jinaai/jina-clip-v2` at 1,024 dimensions, and API (`gemini/gemini-embedding-2`) embedder paths, plus local OCR.
   - **GuardRoute** — agent CRUD, workflow lifecycle/versioning, workflow execution, node executors against real backends.
   - **EvalOps** — dataset management, DeepEval/RAGAS runner execution, eval hub management.

5. **End-to-End Workflow Tests**:
   - Full user journeys (signup → hub → agent/workflow/ingestion → eval) with persistent data retention (deleting test data is not necessary).
   - Multi-user collaboration, hub-linking cross-access, workflow versioning/portability.

6. **Frontend-Backend Contract Tests**:
   - `tests/e2e/contracts/test_api_contracts.py` validating `api.ts` calls against real gateway route schemas.
   - `scripts/extract_api_types.py` parsing TS interfaces into JSON Schema; coverage gap report script.

7. **Live API Tests**:
   - LiteLLM routing & fallback chains, inference proxy, real MCP tool integration, Kafka messaging with offline fallback.

8. **Performance Optimizations**:
   - **Backend** — Redis caching layer (hub, session, model registry, collection metadata), query deduplication (N+1 fixes), DB indexes, cursor pagination, lazy route loading, cache invalidation.
   - **Frontend** — React Query deduplication/staleTime, Zustand state cleanup, component memoization, virtual scrolling, lazy loading, bundle tree-shaking, request batching.

9. **Performance Benchmark Tests**:
   - Response-time assertions, caching effectiveness, concurrent-access isolation.

10. **WebSocket & SSE Streaming Tests**:
    - Telemetry WS/SSE, workflow run SSE, agent invoke SSE, playground SSE, external API SSE — end-to-end against a running gateway (`:8000`) with real Redis pub/sub.

11. **MCP Tools Ecosystem**:
    - User-facing `mcp_tools/` directory with sample tool servers (calculator, web search, code executor) + `_template`.
    - Full MCP lifecycle tests and MCP-node-in-workflow tests.

12. **Comprehensive API Surface Coverage**:
    - Every gateway router endpoint has at least one real integration test; coverage gap audit script.

13. **Test Observability & Logging**:
    - Structured JSON logs per test, `X-Test-Trace-ID` request tracing, failure diagnostics, `pytest-html` report, JUnit XML + JSON CI output.

## Acceptance Criteria
All 17 Base Task groups and their Subtasks marked `[x]`, with:
- All 77 existing tests still passing after reorganization.
- Real-service integration, streaming, E2E, live API, and performance tests passing against actual running dev services and ports (`:8000`, `:8010`, standard DB ports).
- Flaws exposed by tests investigated using Docker container logs and fixed directly in backend/frontend/submodule code.
- Test data persistence handled cleanly without requiring mandatory teardown wipes.
- Frontend optimizations validated via Lighthouse / DevTools before/after metrics.
- Redis cache hit/miss ratios reviewed in gateway logs.
- WebSocket/SSE streams verified stable in browser DevTools.
