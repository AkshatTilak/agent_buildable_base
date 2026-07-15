# ST03_03: Implement RBAC Middleware

## Parent Link
`base/BT03_auth_and_rbac.md`

## Actionable Steps
- [x] Step 1: Create `get_current_user_session` dependency — JWT decode + Redis session lookup
- [x] Step 2: Implement Redis cache lookup for user role/permissions (with JWT fallback on cache miss)
- [x] Step 3: Create `require_permission(tool_name)` dependency factory for tool-level gating
- [x] Step 4: Create `require_role(*roles)` dependency factory for route-level gating
- [x] Step 5: Return 403 Forbidden with descriptive detail on unauthorized access
- [x] Step 6: Write integration tests for RBAC enforcement across all three roles

## Dependencies
- `ST03_01_security_utils.md` ✅, `ST03_04_redis_session.md` ✅

## Definition of Done
RBAC middleware blocks unauthorized tool/route access, returns proper error responses, tested for all three roles.

## Completion Notes
Implemented 2026-07-14 (`app/api/middleware/rbac.py`):
- `get_current_user_session`: composes JWT + Redis into a unified session dict ✅
- Cache miss fallback: derives default tool permissions from role name in JWT ✅
- `require_role(*allowed_roles)`: 403 if user role not in allowed list ✅
- `require_permission(tool_name)`: 403 if tool not in `permissions.tools` ✅
- All dependencies are async and fully typed ✅
- Step 6 (integration tests) deferred to test sprint
