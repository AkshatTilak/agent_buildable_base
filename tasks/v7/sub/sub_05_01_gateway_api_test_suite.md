# Subtask 05_01: Comprehensive Gateway API Integration Test Suite

## Tasks
1. Create `tests/test_v7_gateway_api_suite.py` testing Gateway routes end-to-end using `httpx.AsyncClient` or FastAPI `TestClient`:
   - Auth endpoints (`/auth/register`, `/auth/login`, `/auth/logout`, `/auth/me`).
   - Admin User lifecycle (`/admin/users`, suspend, approve, reject, soft delete, hard delete).
   - Hub Management (`/hubs`, members, links, settings).
   - Ingestion Hub (`/hubs/ingestion/*`, collections, datastores, documents, jobs).
   - Agent Hub (`/hubs/agent/*`, agents, invocation).
   - Workflow Hub (`/hubs/workflow/*`, workflows, runs, execution).
   - Eval Hub (`/hubs/eval/*`, suites, runs, traces).
   - MCP Manager (`/api/mcp/*`).
   - Proxy Endpoints (`/api/qdrant/*`, `/api/neo4j/*`).
2. Ensure tests run completely in memory / local database without requiring heavy container builds.
