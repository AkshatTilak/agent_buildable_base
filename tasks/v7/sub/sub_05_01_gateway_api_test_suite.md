# Subtask 05_01: Comprehensive Gateway API Integration Test Suite

## Tasks
1. [x] Create `tests/test_v7_gateway_api_suite.py` testing Gateway routes end-to-end using FastAPI `TestClient`:
   - [x] Auth endpoints (`/auth/register`, `/auth/login`, `/auth/logout`, `/auth/me`).
   - [x] Admin User lifecycle (`/admin/users`, suspend, approve, reject, soft delete, hard delete).
   - [x] Hub Management (`/api/hubs`, members, links, archive).
   - [x] API Key lifecycle (`/api/settings/api-keys`).
   - [x] Ingestion Hub (`/api/hubs/{id}/ingestion/*`, datastores, collections).
   - [x] Agent Hub (`/api/hubs/{id}/agents`, CRUD + invocation).
   - [x] Workflow Hub (`/api/hubs/{id}/workflows`, drafts, templates).
   - [x] Eval Hub (`/api/hubs/{id}/eval/suites`, cases, cloning).
   - [x] MCP Manager (`/api/mcp/servers`).
   - [x] Proxy Endpoints (`/api/qdrant/*`).
   - [x] Admin invites (`/admin/invites`).
   - [x] Health endpoint (`/health`).
2. [x] Tests run completely in memory using SQLite (`sqlite+aiosqlite:///:memory:`) and mocked external services (Qdrant, Neo4j, inference, Redis, MCP, LiteLLM, proxy backend).
3. [x] Code passes `ruff check` and `mypy` validation.
4. [x] All 12 tests pass: `poetry run pytest tests/test_v7_gateway_api_suite.py -xvs`.

## Notes
- Added `User.is_active` property to `common/models/database.py` because `gateway/api/hubs.py` expects it.
- Fixed `gateway/api/hubs.py` call signatures: `list_links(source_hub_id=...)` and `archive_hub(..., archived=True)`.
- Proxy route depends on `verify_api_key`; test overrides the dependency to inject an admin user while still exercising `require_role("admin")`.
- Mocked proxy `httpx.AsyncClient.request` response must expose a real `content` bytes attribute for Starlette's test transport.
