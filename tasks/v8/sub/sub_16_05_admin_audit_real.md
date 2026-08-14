# Subtask 16_05: Admin Audit Log Real Integration Tests

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `tests/integration/gateway/test_admin_audit_real.py` — audit log.

## Tasks
1. [ ] Perform hub CRUD → verify audit log entries auto-generated.
2. [ ] Query audit log with filters (hub_id, actor, action, date range).
3. [ ] Verify admin-only access enforcement.

## Definition of Done
- Audit log auto-generation, filtered queries, and admin-only access verified against actual running Postgres (`:5432`) and Gateway (`:8000`).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly.
- Deleting test data is not necessary.
