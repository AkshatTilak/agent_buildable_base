# Subtask 16_06: Proxy & Health Real Integration Tests

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `tests/integration/gateway/test_proxy_real.py` + `test_health_real.py` — proxy & health.

## Tasks
1. [x] **Proxy** (`test_proxy_real.py`):
   - Proxy completion request → verify response structure.
   - Proxy with auth → verify API key validation.
   - Test RBAC on proxy endpoints.
2. [x] **Health** (`test_health_real.py`):
   - Gateway health endpoint → verify all service statuses reported.
   - Health endpoint with degraded service → verify partial health report.

## Definition of Done
- Proxy completion, auth, and RBAC verified against actual running Gateway (`:8000`).
- Health endpoint reports all service statuses; degraded service produces partial health report.
- If errors arise, inspect Docker container logs (`docker compose logs`) and fix root causes directly.
- Deleting test data is not necessary.
