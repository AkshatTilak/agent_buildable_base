# ST03_03: Implement RBAC Middleware

## Parent Link
`base/BT03_auth_and_rbac.md`

## Actionable Steps
- [ ] Step 1: Create middleware interceptor that extracts JWT from Authorization header
- [ ] Step 2: Implement Redis cache lookup for user role/permissions (fallback to PostgreSQL)
- [ ] Step 3: Create tool-level permission checker for LangChain tool execution
- [ ] Step 4: Create route-level permission checker for API endpoints
- [ ] Step 5: Return proper 403 responses and agent scratchpad messages for unauthorized access
- [ ] Step 6: Write integration tests for RBAC enforcement

## Dependencies
- `ST03_01_security_utils.md`, `ST03_04_redis_session.md`

## Definition of Done
RBAC middleware blocks unauthorized tool/route access, returns proper error responses, tested for all three roles.
