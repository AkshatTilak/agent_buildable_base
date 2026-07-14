# Auth & RBAC Implementation Reference — EchoMind Core

> Created: 2026-07-14 | Related tasks: BT02, BT03

This document explains the authentication and RBAC design decisions for downstream agents implementing features that need to verify identity or check permissions.

---

## 1. JWT Token Structure

Every access token is a signed HS256 JWT. Payload fields:

| Field | Type | Description |
|---|---|---|
| `sub` | `str` (UUID) | User's database UUID as string |
| `email` | `str` | User's email address |
| `role` | `str` | Role name: `"standard"` / `"premium"` / `"admin"` |
| `exp` | `int` | Unix timestamp — expiry (now + 30 min) |
| `iat` | `int` | Unix timestamp — issued at |

**Signing**: `HS256` with `settings.JWT_SECRET_KEY`. Change this in production via `.env`.

---

## 2. Redis Session Schema

After login or register, a Redis hash is written:

```
KEY:    session:{user_id}       (e.g., session:550e8400-e29b-41d4-a716-446655440000)
TYPE:   Hash
TTL:    1800 seconds (= JWT expiry)

FIELDS:
  role_id     → "1"  (string-encoded integer)
  role_name   → "standard"
  permissions → '{"max_queries_per_min": 10, "tools": ["vector_search", "ragflow_retrieval"]}'
```

Chat history uses a separate key namespace:

```
KEY:    chat:{session_id}
TYPE:   Redis List (managed by LangChain's RedisChatMessageHistory)
TTL:    3600 seconds (1 hour of inactivity)
```

Rate limiter uses per-minute buckets:

```
KEY:    ratelimit:{user_id}:{minute_epoch}
TYPE:   String (integer counter)
TTL:    60 seconds
```

---

## 3. RBAC Dependency Injection Pattern

All protected endpoints use FastAPI's `Depends()` system. There are two main patterns:

### Pattern A — Just authenticate:
```python
from app.common.security import get_current_user

@router.get("/protected")
async def my_endpoint(
    token_payload: Annotated[dict, Depends(get_current_user)]
) -> ...:
    user_id = token_payload["sub"]
    role = token_payload["role"]
```

### Pattern B — Authenticate + Redis session + RBAC:
```python
from app.api.middleware.rbac import get_current_user_session, require_role, require_permission

# Role gate:
@router.get("/admin-only", dependencies=[Depends(require_role("admin"))])
async def admin_endpoint(): ...

# Tool permission gate:
@router.post("/analytics", dependencies=[Depends(require_permission("user_analytics"))])
async def analytics_endpoint(): ...

# Full session (includes permissions dict):
@router.post("/chat")
async def chat(session: Annotated[dict, Depends(get_current_user_session)]) -> ...:
    allowed_tools = session["permissions"]["tools"]
```

---

## 4. Permission → Role Mapping (mirrored from DB seed)

| Role | `max_queries_per_min` | `tools` |
|---|---|---|
| `standard` | 10 | `vector_search`, `ragflow_retrieval` |
| `premium` | 50 | `vector_search`, `ragflow_retrieval`, `user_analytics` |
| `admin` | 100 | `vector_search`, `ragflow_retrieval`, `user_analytics`, `diagnostics`, `user_management` |

---

## 5. Auth Endpoint Summary

| Method | Path | Auth Required | Description |
|---|---|---|---|
| `POST` | `/api/v1/auth/register` | ❌ | Create account → 201 UserResponse |
| `POST` | `/api/v1/auth/login` | ❌ | Get JWT → 200 TokenResponse |
| `GET` | `/api/v1/auth/me` | ✅ Bearer | Current user profile → 200 UserResponse |
| `POST` | `/api/v1/auth/logout` | ✅ Bearer | Invalidate Redis session → 204 |
| `POST` | `/api/v1/chat` | ✅ Bearer + Rate Limit | AI agent query → 200 ChatResponse |

---

## 6. Error Codes

| HTTP | Trigger |
|---|---|
| 401 | Missing/expired/invalid JWT |
| 403 | Valid JWT but insufficient role/tool permission |
| 409 | Email already registered |
| 429 | Rate limit exceeded (Retry-After: 60 header set) |
| 500 | DB missing default role seed |
