# Security Policies & Guardrails

> **Source:** Migrated from `requirements/security.md`
> **Last Updated:** 2026-07-14

This document details API security, data protection, LLM safety guardrails, and sandbox isolation requirements across the ContAIned AI Platform.

---

## 1. Tenancy Isolation & IDOR Prevention (V6) — highest priority

V6 makes the **Hub** the unit of tenancy. Every domain resource belongs to exactly one hub, so the
single largest security risk on the platform is now **cross-tenant data exposure (IDOR)**. The tenancy
model itself is defined in
[`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md); this section
states the non-negotiable security obligations that follow from it.

### 1.1 Every hub-scoped query MUST filter by `hub_id`

A lookup by primary key alone on a hub-scoped model is a **defect**, not a style preference. Every
repository/service function touching a hub-scoped table takes `hub_id` as a **required** argument and
puts it in the `WHERE` clause (`hubs.md` §5.3):

```python
# CORRECT
stmt = select(AgentDefinition).where(
    AgentDefinition.id == agent_id,
    AgentDefinition.hub_id == ctx.hub.id,
)

# FORBIDDEN — IDOR vulnerability
stmt = select(AgentDefinition).where(AgentDefinition.id == agent_id)
```

The same rule applies to `UPDATE` and `DELETE`. A resource id supplied by the client is never
sufficient authority on its own.

### 1.2 Non-members get `404`, never `403`

Hub existence must not be enumerable. The `require_hub()` dependency deliberately collapses
"does not exist" and "exists but you are not a member" into the same response (`hubs.md` §5.2):

| Condition | Response |
|---|---|
| Hub does not exist | `404 Hub not found` |
| Hub exists, caller is not a member and not a platform admin | `404 Hub not found` — **never** `403` |
| Caller is a member but the hub role is insufficient | `403 Insufficient hub role` |
| Hub type mismatch for the route | `404 Hub not found` |
| Hub archived and the request is mutating | `409 Hub is archived` |

`403` is only ever returned once membership is already established, so it leaks nothing new.

### 1.3 Cross-hub access requires an explicit link

Hubs are isolated by default. A resource in hub *A* may reference a resource in hub *B* **only** when a
`hub_links` row grants it. Links are:

* **explicit** — created deliberately by a user with `owner`/`maintainer` on the source hub and at
  least `contributor` on the target;
* **directional** — `source_hub_id → target_hub_id` only; the reverse direction is a separate grant;
* **non-transitive** — a workflow hub linked to an agent hub does **not** inherit that agent hub's
  ingestion links;
* **re-validated at execution time**, not only at save time, via `common/services/hub_resolver.py`.
  Revoking a link immediately breaks dependent executions with a descriptive error rather than
  silently continuing to serve data.

### 1.4 Hub-scoped API keys

`api_keys.hub_id` is nullable. A key with `hub_id` set may reach **only** that hub; attempting to
invoke a resource in any other hub fails as if the resource did not exist. A key with `hub_id = null`
is platform-wide and is bounded by the permissions of its owning user. See
[`external_api.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/external_api.md) §5.

### 1.5 Static guard test

`tests/test_hub_isolation.py` statically inspects `gateway/` and `projects/` and **fails the build** if
any hub-scoped model is queried by primary key without an accompanying `hub_id` predicate. New
hub-scoped models must be added to its model list in the same change that introduces them.

---

## 2. API Security ✅

### Authentication Middleware
- API key validation middleware on the gateway.
- API keys passed via `X-API-Key` header.
- Toggle: `AUTH_ENABLED=false` (default for local dev) / `AUTH_ENABLED=true` (production).
- When disabled: all requests allowed without a key.
- When enabled: reject without valid key → `401 Unauthorized`.
- Store valid API keys in PostgreSQL (`api_keys` table) or environment variable.

### Rate Limiting
- Applied on gateway using `slowapi` or `fastapi-limiter`.
- Default limits:
  - General endpoints: **100 req/min** per IP.
  - Inference-proxied endpoints: **30 req/min** per IP.
  - File upload endpoints: **10 req/min** per IP.
- Authentication & invitation limits (V6, canonical in
  [`user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) §7):

| Route | Limit | Keyed by |
|---|---|---|
| `POST /auth/login` | 10 / min | IP |
| `POST /auth/register` | 5 / hour | IP |
| `POST /auth/forgot-password` | 3 / hour | email |
| `POST /admin/invites` | 60 / hour | admin user |
| `POST /admin/invites/{id}/resend` | 3 / hour | invite |

- Returns `429 Too Many Requests` with `Retry-After` header.
- Rate limit state stored in Redis for distributed deployments.

### CORS Configuration
- Configurable allowlist (not `allow_origins=["*"]`).
- Default: `["http://localhost:3000", "http://localhost:5173"]`.
- Read from `CORS_ORIGINS` environment variable.

### Request Validation
- Max request body size: 100 MB (file uploads), 1 MB (JSON payloads).
- Validate `Content-Type` headers on upload endpoints.
- Sanitize filename inputs to prevent path traversal.

---

## 3. Data Security (Pending Implementation)

### Secret Management
- `.env` file in `.gitignore`.
- `.env.example` with placeholder values only.
- Production: Docker secrets or cloud secret manager.
- API keys, DB passwords, provider keys must never appear in logs.

### Datastore Binding Credentials (V6)
`datastore_bindings.credentials_encrypted` holds per-hub credentials for user-supplied Qdrant / Neo4j /
Postgres / OpenSearch instances
([`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.4):

- Encrypted at rest with **Fernet**, keyed by `DATASTORE_ENCRYPTION_KEY`.
- **Never** serialised into any API response — not on create, not on read, not in error payloads,
  not in audit `before_json` / `after_json`.
- `connection_uri` is returned **masked** (credentials segment replaced with `***`).
- Decrypted only in-process, immediately before opening a connection; never logged.
- Rotating `DATASTORE_ENCRYPTION_KEY` requires a re-encryption migration; the key itself is a
  deployment secret, never committed.

### Invite & Password-Reset Tokens (V6)
- **256-bit entropy** (`secrets.token_urlsafe(32)`).
- Only the **SHA-256 hash** is persisted (`user_invites.token_hash`); the raw token is never stored.
- **Single-use** — redeeming marks the record `accepted`/consumed; a replay returns `409`.
- **TTL-bound** — `INVITE_TTL_HOURS` (default 72), `PASSWORD_RESET_TTL_MINUTES` (default 60);
  expired tokens return `410`.
- Lookup is by hash only, and comparison uses `hmac.compare_digest`.
- The raw token is surfaced exactly once, only to the creating admin, and only when SMTP delivery
  failed (the "copy invite link" fallback).

### PII Handling
- Ingested documents may contain PII. Ensure PII is not leaked in:
  - API responses (post-flight PII scanning).
  - Log messages (redact sensitive content).
  - Kafka trace payloads (strip PII before publishing).
- EvalOps includes PII leakage detection tests.

### Database Security
- Parameterized queries exclusively — no string-formatted SQL.
- PostgreSQL: dedicated user with minimal privileges (not superuser).
- Neo4j: read-only mode enforcement in `common/clients/neo4j.py`.
- Qdrant: use `QDRANT_API_KEY` when exposed to network.

---

## 4. LLM Safety Guardrails (Pending Implementation)

### Pre-Flight Input Validation
- **Prompt Injection Detection:** Scan for known injection patterns. Check for system prompt override attempts. Block or flag suspicious inputs.
- **Input Sanitization:** Strip HTML/script tags. Enforce maximum prompt length per model context window.

### Post-Flight Output Validation
- **PII Scrubbing:** Scan output for emails, phone numbers, API keys, SSNs. Redact before returning to user.
- **Toxicity Check:** Optional classifier with configurable threshold (default: 0.1).
- **Hallucination Grounding:** When using RAG, validate response references retrieved context.

### MCP / Tool Security
- `query_database` tool rejects queries on tables not starting with project prefix (e.g., `syntraflow_`).
- `query_graph` tool rejects Cypher containing write operations.
- All MCP tools use parameterized queries — no raw string interpolation.

---

## 5. Code Execution Sandbox (GuardRoute Coding Agent) ✅

### Sandbox Implementation
- Uses `RestrictedPython` (AST-level restrictions).
  - **Blocked:** file I/O, subprocess, network access, `__import__`, `eval`, `exec`.
  - **Allowed:** math, string operations, list comprehensions, basic data structures.
- Execution timeout: 10 seconds.
- Memory limit to prevent memory bombs.

### Future Upgrade Path
- Container-based sandboxing via `docker-py` (ephemeral containers).
- Alternative: WASM-based sandbox via `pyodide`.

### Sandbox Output Capture
- Captures `stdout` and `stderr`.
- Returns output in `SubAgentResult` schema.
- Truncates to max 10,000 characters.

---

## 6. Network Security (Pending Implementation)

### Internal Service Communication
- Services within docker-compose communicate via internal Docker network.
- Only gateway and admin tools expose ports to host.
- Inference server NOT exposed externally — only via gateway.

### External API Calls
- All outbound API calls (Gemini, OpenRouter, Groq) use HTTPS.
- Strict timeouts:
  - LLM completion: 60 seconds.
  - Web search agent: 2 seconds.
  - Inference server internal: 120 seconds.
- Provider outages handled via fallback chains.

---

## 7. Audit & Compliance (Pending Implementation)

### Audit Log (V6) — mandatory
Every mutating **hub-scoped** endpoint and every **admin** endpoint writes **exactly one**
`audit_log` row
([`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.5):

- `actor_user_id` — the authenticated user (or the owning user of an API key).
- `ip_address` — the originating client IP.
- `hub_id` — the hub the action targeted; `null` for platform-level actions.
- `before_json` / `after_json` — the resource state either side of the change.
- Secrets (`credentials_encrypted`, `password_hash`, `token_hash`, API key material) are stripped from
  both snapshots before writing.
- Writing the row is part of the same transaction as the mutation: if the audit write fails, the
  mutation is rolled back.
- The log is append-only; there is no update or delete route. It is readable only at `/admin/audit`
  by a platform `admin`.

### Request Logging
- Log all API requests (method, path, status code, latency).
- Do NOT log request/response bodies by default (PII risk).
- Optionally enable body logging in development mode only.

### Security Event Logging
- Log authentication failures (invalid API keys).
- Log rate limit violations.
- Log prompt injection detection triggers.
- Log sandbox execution attempts and outcomes.
