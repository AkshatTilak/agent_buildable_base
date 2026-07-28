# Authentication & RBAC Architecture

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

> **V6 UPDATE:** User lifecycle (registration, invitation, approval, suspension) is defined canonically
> in [`user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md).
> The role model is defined canonically in
> [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §4.
> Where this document disagrees with either, those documents win. This file describes how the gateway
> auth layer implements them.

---

## 1. Overview

Auth is **opt-in** via `AUTH_ENABLED=true` (default: `false`). When enabled, users authenticate via
Google OAuth2, GitHub OAuth2, **or a local email + password**. JWTs are issued for session management.

Authorisation in V6 is split across **two independent axes**. The V5 global `admin` / `editor` /
`viewer` triple is **removed**:

* **Platform role** — `users.platform_role` ∈ `admin | member`.
* **Hub role** — `hub_members.hub_role` ∈ `owner | maintainer | contributor | viewer`, held per hub.

Access additionally requires an **account status** of `active` (see §5.2).

---

## 2. OAuth2 Flow

```
User → GET /auth/login/{provider} → Redirect to IdP (PKCE + signed state) → GET /auth/callback
  → Resolve identity → Apply invite / approval gate → Issue JWT (only if status = "active") → Frontend
```

- Library: `authlib` (Starlette integration)
- Providers: `google`, `github`
- The provider email **must be verified**; an unverified email is rejected outright
- First registered user auto-assigned `platform_role = "admin"` and `status = "active"`
- Subsequent self-registrations land in `status = "pending"` unless an invite matches (see §5.3)

### 2.1 Identity linking

V5 stored a single `users.provider` / `users.provider_id` pair. V6 **drops both columns** and replaces
them with a `user_identities` table (`provider` ∈ `google | github | password`), so one account may hold
several sign-in methods simultaneously. Accounts are keyed by **verified, normalised email**
(trimmed + lowercased before any comparison or storage).

Resolution algorithm on every OAuth callback and password registration:

1. A `user_identities` row matches `(provider, provider_id)` → load that user, touch `last_used_at`.
2. No identity row, but a `users` row exists with the same normalised email → **link**: insert a new
   `user_identities` row against that existing user. This is how a user who signed up with Google can
   later sign in with GitHub, or set a password, on the same account.
3. No user at all → look for a `pending` `user_invites` row matching the email:
   * **Invite found** → create the user with the invite's `platform_role`, materialise its
     `hub_grants_json` as `hub_members` rows, set `status = "active"`, mark the invite `accepted`.
   * **No invite** → create the user with `platform_role = "member"` and `status = "pending"`.
4. Issue a session **only** when `status == "active"`.

Linking is performed only against a **verified** provider email; an unverified email never joins an
existing account.

### 2.2 Local password authentication

- Hash: **Argon2id** (`time_cost=3, memory_cost=65536, parallelism=4`). Never bcrypt, MD5 or bare SHA.
- Policy: **minimum 12 characters**, rejected against a common-password **denylist**.
- Passwords are never logged, never echoed, never present in any response body.
- **Lockout:** `users.failed_login_count` increments on each failure; at **5** failures the account is
  locked via `locked_until = now + 15 minutes`.
- `/auth/login`, `/auth/register` and `/auth/forgot-password` return **constant-time, non-enumerating**
  responses — "unknown email" and "wrong password" are indistinguishable to the caller.
- Reset tokens are single-use, TTL-bound (`PASSWORD_RESET_TTL_MINUTES`, default `60`), persisted as
  SHA-256 hashes only, and compared with `hmac.compare_digest`.

---

## 3. JWT Token Structure

```json
{
  "sub": "user-uuid",
  "email": "user@example.com",
  "platform_role": "admin",
  "status": "active",
  "exp": 1234567890,
  "iat": 1234567890
}
```

- Algorithm: HS256 (configurable via `JWT_ALGORITHM`)
- Expiry: 24 hours (configurable via `JWT_EXPIRY_HOURS`)
- Stored: `UserSession` table (hashed) for server-side invalidation
- The `role` claim from V5 is replaced by `platform_role`
- **Hub roles are deliberately NOT embedded in the token.** They are resolved per request from
  `hub_members` by the `require_hub()` dependency
  ([`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §5.2), so a
  membership change takes effect immediately instead of at the next token refresh.

### Session revocation triggers

All `user_sessions` rows for a user are deleted immediately on any of:

| Trigger | Scope |
|---|---|
| Password change (`POST /auth/change-password`) | all sessions except the calling one |
| Password reset completion | all sessions |
| Admin **suspend** (`active → suspended`) | all sessions |
| Admin **reject** (`pending → rejected`) | all sessions |
| Platform role demotion (`admin → member`) | all sessions |

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

## 5. Roles, Account Status & Admission

### 5.1 Two-axis role model (V6)

Canonical definition:
[`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §4.

**Platform role** — `users.platform_role`, replaces the V5 `users.role` column:

| Platform role | Meaning |
|---|---|
| `admin` | Superuser. Implicit `owner` on every hub. Manages users, invites, approvals, platform settings, MCP Registry, infrastructure. |
| `member` | Standard user. Sees only the hubs they are a member of. May create hubs if `ALLOW_MEMBER_HUB_CREATION` is enabled. |

**Hub role** — `hub_members.hub_role`, held once per `(hub_id, user_id)`:
`owner (4) > maintainer (3) > contributor (2) > viewer (1)`. A check for `contributor` is satisfied by
`maintainer` and `owner`. A platform `admin` short-circuits every hub check and is treated as `owner`.
The per-capability matrix lives in `hubs.md` §4.2 and is not duplicated here.

**V5 → V6 migration mapping** for the removed global triple:

| V5 `users.role` | V6 `users.platform_role` |
|---|---|
| `admin` | `admin` |
| `editor` | `member` |
| `viewer` | `member` |

V5 `editor` / `viewer` capability is expressed per hub instead: existing users are seeded into the four
default hubs as `contributor` / `viewer` respectively (`hubs.md` §6 step 6).

### 5.2 Account status

`users.is_active` (boolean) is **replaced** by `users.status` ∈ `pending | active | suspended | rejected`.

Only `status = "active"` users receive a usable session token. Every other status returns `403` with a
machine-readable `reason` so the frontend can render the correct holding screen:

| Status | Reason code |
|---|---|
| `pending` | `ACCOUNT_PENDING_APPROVAL` |
| `suspended` | `ACCOUNT_SUSPENDED` |
| `rejected` | `ACCOUNT_REJECTED` |

### 5.3 Approval gate

A user who self-registers — by OAuth **or** by password signup — **without a matching `pending` invite**
is created with `status = "pending"` and receives `403 ACCOUNT_PENDING_APPROVAL` instead of a session.
A platform admin then approves (`pending → active`) or rejects (`pending → rejected`) them from
`/admin/users/pending`.

Bypasses:

* `AUTO_APPROVE_EMAIL_DOMAINS` — optional CSV of domains (e.g. `acme.com`); a verified email in one of
  those domains skips the gate and is created `active`.
* A matching invite — the invite already encodes the intended role and hub grants, so no approval is needed.
* `ALLOW_SELF_REGISTRATION=false` disables self sign-up entirely; only invited users may join.

### 5.4 Invite redemption

`GET /auth/invite/{token}` returns a **preview only** (email, inviter, platform role, hub names) and
creates nothing. The frontend then offers two paths:

1. **Continue with Google / GitHub** — the OAuth flow carries the invite token inside the signed
   `state`. The verified provider email **must** equal the invite email, otherwise
   `409 Invite email mismatch`.
2. **Set a password** — `POST /auth/invite/{token}/accept` with a password meeting the §2.2 policy.

Either path creates an `active` user, writes the `user_identities` row, materialises
`hub_grants_json` into `hub_members`, marks the invite `accepted`, and writes one `audit_log` row.
Re-redeeming an already-`accepted` token returns `409`; an expired token returns `410`.
`hub_grants_json` is re-validated against real hub ids at redemption time, not only at creation.

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

# --- V6: password auth, invites, approval gate ---
INVITE_TTL_HOURS=72
PASSWORD_RESET_TTL_MINUTES=60
APP_PUBLIC_URL=http://localhost:5173
ALLOW_SELF_REGISTRATION=true
ALLOW_MEMBER_HUB_CREATION=true
AUTO_APPROVE_EMAIL_DOMAINS=          # optional CSV, e.g. "acme.com" -> skip the approval gate
```

SMTP settings for invite / approval / reset email are listed in
[`user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) §5.

---

## 7. Database Tables

- `users` — account record: `id`, `email`, `display_name`, `avatar_url`, `platform_role`, `status`,
  `password_hash`, `password_updated_at`, `approved_by`, `approved_at`, `failed_login_count`,
  `locked_until`, `created_at`, `last_login`.
  **Removed in V6:** `role`, `is_active`, `provider`, `provider_id`.
- `user_identities` — one row per sign-in method: `id`, `user_id`, `provider`
  (`google | github | password`), `provider_id`, `email`, `created_at`, `last_used_at`;
  `UNIQUE (provider, provider_id)`.
- `user_invites` — admin-issued invitations: `id`, `email`, `token_hash`, `platform_role`,
  `hub_grants_json`, `invited_by`, `status`, `expires_at`, `accepted_at`, `accepted_user_id`,
  `resend_count`, `last_sent_at`, `created_at`.
- `hub_members` — per-hub authorisation: `hub_id`, `user_id`, `hub_role`, `invited_by`
  (see [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.2).
- `user_sessions` — active JWT sessions (id, user_id, token_hash, expires_at, created_at).
- `audit_log` — every auth and admin mutation writes exactly one row.

Column-level definitions are canonical in
[`user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) §2.

---

## 8. Dependencies

```toml
auth = [
    "authlib (>=1.3.0,<2.0.0)",
    "python-jose[cryptography] (>=3.3.0,<4.0.0)",
    "argon2-cffi (>=23.1.0,<24.0.0)",
]
```

---

## 9. `/auth` Route Table (V6)

| Method | Path | Auth | Purpose |
|---|---|---|---|
| `GET` | `/auth/login/{provider}` | public | Start Google/GitHub OAuth (PKCE + signed state) |
| `GET` | `/auth/callback` | public | OAuth callback; identity resolution per §2.1 |
| `POST` | `/auth/register` | public | Password sign-up; invite / approval-gate branch per §5.3 |
| `POST` | `/auth/login` | public | Password sign-in; Argon2id verify, lockout, constant-time response |
| `POST` | `/auth/logout` | session | Delete the current `user_sessions` row |
| `GET` | `/auth/me` | session | Current user, platform role, status, hub memberships |
| `POST` | `/auth/forgot-password` | public | Issue a single-use reset token by email; always `202` |
| `POST` | `/auth/reset-password` | public | Consume the reset token, set a new password, revoke all sessions |
| `POST` | `/auth/change-password` | session | Requires the current password; revokes all other sessions |
| `GET` | `/auth/invite/{token}` | public | Invite **preview** — creates nothing |
| `POST` | `/auth/invite/{token}/accept` | public | Redeem an invite by setting a password |

Admin-side user, invite and approval routes live under `/admin` and are documented in
[`user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) §4.

Rate limits for these routes: `/auth/login` 10/min per IP, `/auth/register` 5/hour per IP,
`/auth/forgot-password` 3/hour per email — see
[`security.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md) §2.
