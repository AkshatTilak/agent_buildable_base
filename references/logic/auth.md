# Authentication & RBAC Architecture

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

## 1. Overview

Auth is **opt-in** via `AUTH_ENABLED=true` (default: `false`). When enabled, users authenticate via Google or GitHub OAuth2. JWTs are issued for session management. Three roles enforce access control: `admin`, `editor`, `viewer`.

---

## 2. OAuth2 Flow

```
User → GET /auth/login/google → Redirect to Google → Callback → Upsert User → Issue JWT → Redirect to Frontend
User → GET /auth/login/github → Redirect to GitHub → Callback → Upsert User → Issue JWT → Redirect to Frontend
```

- Library: `authlib` (Starlette integration)
- First registered user auto-assigned `admin` role
- Subsequent users get `viewer` role (admin can upgrade)
- Users with same email across providers are linked to the same account

---

## 3. JWT Token Structure

```json
{
  "sub": "user-uuid",
  "email": "user@example.com",
  "role": "admin",
  "exp": 1234567890,
  "iat": 1234567890
}
```

- Algorithm: HS256 (configurable via `JWT_ALGORITHM`)
- Expiry: 24 hours (configurable via `JWT_EXPIRY_HOURS`)
- Stored: `UserSession` table (hashed) for server-side invalidation

---

## 4. Middleware Architecture

### JWT Auth Middleware (Dashboard Routes)
- Applied to all `/api/*` routes
- Skips: `/auth/*`, `/health`, `/api/health/*`
- Validates JWT from `Authorization: Bearer <token>` or `auth_token` cookie
- Sets `request.state.user`

### API Key Middleware (External API Routes)
- Applied to `/v1/*` routes only
- Validates API key from `Authorization: Bearer sk-...` header
- Separate from JWT — external consumers don't need OAuth

---

## 5. Role Permissions

| Role | Dashboard | Create/Edit | Settings | User Mgmt |
|---|---|---|---|---|
| `admin` | ✅ | ✅ | ✅ | ✅ |
| `editor` | ✅ | ✅ | ❌ | ❌ |
| `viewer` | ✅ (read-only) | ❌ | ❌ | ❌ |

---

## 6. Environment Variables

```env
AUTH_ENABLED=false
GOOGLE_CLIENT_ID=""
GOOGLE_CLIENT_SECRET=""
GITHUB_CLIENT_ID=""
GITHUB_CLIENT_SECRET=""
JWT_SECRET_KEY=""
JWT_ALGORITHM="HS256"
JWT_EXPIRY_HOURS=24
```

---

## 7. Database Tables

- `users` — user identity (id, email, display_name, avatar_url, provider, provider_id, role, is_active, created_at, last_login)
- `user_sessions` — active JWT sessions (id, user_id, token_hash, expires_at, created_at)

---

## 8. Dependencies

```toml
auth = [
    "authlib (>=1.3.0,<2.0.0)",
    "python-jose[cryptography] (>=3.3.0,<4.0.0)",
]
```
