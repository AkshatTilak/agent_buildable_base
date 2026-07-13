# BT03: Authentication & Role-Based Access Control

## Objective
Implement user registration, JWT-based authentication, and fine-grained RBAC enforcement at both API and tool-execution levels.

## Business/System Value
Critical security foundation. Without RBAC, agents cannot differentiate user permissions, and tool execution is unprotected.

## Subtask Registry
- [ ] `sub/ST03_01_security_utils.md` — Implement password hashing (bcrypt) and JWT utilities
- [ ] `sub/ST03_02_auth_endpoints.md` — Implement register, login, and profile API endpoints
- [ ] `sub/ST03_03_rbac_middleware.md` — Implement RBAC middleware interceptor for API routes and tool calls
- [ ] `sub/ST03_04_redis_session.md` — Implement Redis session caching for JWT/role lookups

## Complexity Rating
**High** — Security-critical; requires careful JWT handling and Redis cache synchronization.

## Source Code Location
`../../echomind-core/app/api/routes/auth.py`, `../../echomind-core/app/api/middleware/rbac.py`, `../../echomind-core/app/common/security.py`, `../../echomind-core/app/cache/session_cache.py`
