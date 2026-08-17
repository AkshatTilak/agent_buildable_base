# Base Task 16: Comprehensive API Surface Coverage (v8)

## Objective
Ensure every single gateway API endpoint has at least one real integration test. Audit all 16 gateway routers + 3 submodule routers and fill gaps.

## Why
Many gateway endpoints have minimal or no test coverage. A coverage audit plus targeted integration tests ensures every endpoint is exercised against real services.

## Full API inventory (from `gateway/api/__init__.py`)

| Router | Prefix | Endpoints | Current Test Coverage |
|--------|--------|-----------|----------------------|
| `hubs` | `/api/hubs` | CRUD, members, links, bindings | Partial (mock-only) |
| `ingestion_hub` | `/api/hubs/{id}/ingestion` | Upload, jobs, documents, chunks | Partial (mock-only) |
| `workflows` | `/api/hubs/{id}/workflows` | CRUD, versions, runs, stream | Partial (mock-only) |
| `eval_hub` | `/api/hubs/{id}/eval` | Datasets, runs, test cases, dashboard | Partial (mock-only) |
| `agent_crud` | `/api/hubs/{id}/agents` | CRUD | Partial |
| `agent_invoke` | `/api/hubs/{id}/agents/{id}/invoke` | Invoke, stream | Minimal |
| `playground` | `/api/playground` | Chat, stream | Minimal |
| `mcp_manager` | `/api/mcp` | Servers, tools, invoke, db-tools | Minimal |
| `models` | `/api/models` | Registry, select, register, delete, download | Minimal |
| `api_keys` | `/api/settings/api-keys` | CRUD, stats | Minimal |
| `credentials` | `/api/settings/credentials` | Provider key management | Minimal |
| `db_credentials` | `/api/hubs/{id}/db-credentials` | External DB vault | Minimal |
| `admin_users` | `/api/admin/users` | User management | Partial |
| `admin_audit` | `/api/admin/audit` | Audit log queries | None |
| `telemetry` | `/api/telemetry` | WS, SSE stream | Covered in Task 14 |
| `proxy` | `/api/proxy` | LiteLLM proxy | Minimal |
| `external` | `/v1/chat/completions` | OpenAI-compatible API | Minimal |
| `auth` | `/auth` | Login, register, OAuth, sessions | Partial |
| `health` | `/health` | Health checks | Partial |

## Scope
- **`tests/integration/gateway/test_api_keys_real.py`** — Full API key lifecycle:
  - Create key → verify hashed key in DB → use key for auth → verify usage count increments.
  - Hub-scoped key (only works for requests within that hub).
  - Revoke key → verify auth fails.
  - Rate limit enforcement per key.
  - Key stats/analytics endpoint.
- **`tests/integration/gateway/test_credentials_real.py`** — Provider credentials:
  - Set Google API key via API → verify encrypted in DB → verify `mask_key` in response.
  - Override env-based key with DB key → verify DB takes precedence.
  - Delete DB key → verify falls back to env.
  - List all provider slots (env + DB merged view).
- **`tests/integration/gateway/test_db_credentials_real.py`** — External DB credentials vault:
  - Create Postgres credential → verify encrypted payload in DB.
  - Test connection → verify success/failure response.
  - Generate DB MCP tools for credential → verify tool definitions.
  - Update credential → verify re-encryption.
  - Delete credential → verify cleanup.
- **`tests/integration/gateway/test_models_real.py`** — Model registry:
  - List available models (LiteLLM + local) → verify response schema.
  - Register custom model → verify DB row.
  - Select model for role → verify active model changes.
  - Delete/download model endpoints.
  - Test model health probe.
- **`tests/integration/gateway/test_admin_audit_real.py`** — Audit log:
  - Perform hub CRUD → verify audit log entries auto-generated.
  - Query audit log with filters (hub_id, actor, action, date range).
  - Verify admin-only access enforcement.
- **`tests/integration/gateway/test_proxy_real.py`** — LiteLLM proxy:
  - Proxy completion request → verify response structure.
  - Proxy with auth → verify API key validation.
  - Test RBAC on proxy endpoints.
- **`tests/integration/gateway/test_health_real.py`** — Health checks:
  - Gateway health endpoint → verify all service statuses reported.
  - Health endpoint with degraded service → verify partial health report.
- **Coverage gap report** — `scripts/api_coverage_audit.py` — Script that parses all FastAPI routers and compares against test files to produce a coverage matrix showing untested endpoints.

## Associated Subtasks
1. `[x]` `sub_16_01_api_keys_real.md`: `test_api_keys_real.py` — API key lifecycle.
2. `[x]` `sub_16_02_credentials_real.md`: `test_credentials_real.py` — provider credentials.
3. `[x]` `sub_16_03_db_credentials_real.md`: `test_db_credentials_real.py` — external DB vault.
4. `[x]` `sub_16_04_models_real.md`: `test_models_real.py` — model registry.
5. `[x]` `sub_16_05_admin_audit_real.md`: `test_admin_audit_real.py` — audit log.
6. `[x]` `sub_16_06_proxy_health_real.md`: `test_proxy_real.py` + `test_health_real.py` — proxy & health.
7. `[x]` `sub_16_07_api_coverage_audit.md`: `scripts/api_coverage_audit.py` — coverage matrix.

## Definition of Done
- Every gateway router endpoint has at least one real integration test against actual running Gateway (`:8000`) and backend services.
- API key, credentials, DB credentials, models, audit, proxy, and health tests pass against actual running services on standard ports.
- `scripts/api_coverage_audit.py` produces a coverage matrix showing untested endpoints.
- If errors arise, inspect Docker container logs (`docker compose logs db redis`) and fix root causes directly.
- Deleting test data is not necessary.
