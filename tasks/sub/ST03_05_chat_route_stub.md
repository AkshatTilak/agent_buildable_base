# ST03_05: Create RBAC-Protected Chat Route Stub

## Parent Link
`base/BT03_auth_and_rbac.md`

## Context
The `references/logic/agent_flow.md` defines `POST /api/v1/chat` as the primary user-facing endpoint. This route requires authentication (JWT) and RBAC enforcement (all roles allowed, but rate-limited per role). The actual LangChain orchestrator wiring is deferred to BT04 (ST04_01_orchestrator.md). This subtask creates the route with proper security dependencies and a placeholder orchestrator call.

## Actionable Steps
- [x] Step 1: Create `app/api/routes/chat.py` with `POST /api/v1/chat` endpoint
- [x] Step 2: Apply `get_current_user` JWT dependency to the endpoint
- [x] Step 3: Apply `RateLimiter.is_allowed()` check using the user's role
- [x] Step 4: Accept `ChatRequest` Pydantic body (query: str, session_id: str)
- [x] Step 5: Return `ChatResponse` stub (deferred orchestrator call stubbed with TODO)
- [x] Step 6: Register `/api/v1/chat` router in `app/main.py`

## Dependencies
- `ST03_01_security_utils.md` — `get_current_user` dependency
- `ST03_03_rbac_middleware.md` — `RateLimiter` class
- `ST03_04_redis_session.md` — Redis pool for rate limiter
- BT04 `orchestrator.py` will replace the stub response with real agent execution

## Definition of Done
`POST /api/v1/chat` is accessible, returns 401 without a valid JWT, returns 429 when rate limit exceeded, and returns a stub response with a `response` field for authenticated users.
