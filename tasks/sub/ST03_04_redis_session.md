# ST03_04: Implement Redis Session Caching

## Parent Link
`base/BT03_auth_and_rbac.md`

## Actionable Steps
- [ ] Step 1: Initialize Redis connection pool from REDIS_URL
- [ ] Step 2: Implement `cache_user_session()` — store role_id and permissions with TTL
- [ ] Step 3: Implement `get_user_session()` — retrieve cached session data
- [ ] Step 4: Wire session caching into login endpoint (cache on successful auth)
- [ ] Step 5: Wire session lookup into RBAC middleware

## Dependencies
- `redis[hiredis]` installed, Redis container running

## Definition of Done
Session caching operational, RBAC middleware reads from Redis with PostgreSQL fallback.
