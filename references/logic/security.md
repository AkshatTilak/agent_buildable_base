# Security Policies & Guardrails

> **Source:** Migrated from `requirements/security.md`
> **Last Updated:** 2026-07-14

This document details API security, data protection, LLM safety guardrails, and sandbox isolation requirements across the ContAIned AI Platform.

---

## 1. API Security ✅

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

## 2. Data Security (Pending Implementation)

### Secret Management
- `.env` file in `.gitignore`.
- `.env.example` with placeholder values only.
- Production: Docker secrets or cloud secret manager.
- API keys, DB passwords, provider keys must never appear in logs.

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

## 3. LLM Safety Guardrails (Pending Implementation)

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

## 4. Code Execution Sandbox (GuardRoute Coding Agent) ✅

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

## 5. Network Security (Pending Implementation)

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

## 6. Audit & Compliance (Pending Implementation)

### Request Logging
- Log all API requests (method, path, status code, latency).
- Do NOT log request/response bodies by default (PII risk).
- Optionally enable body logging in development mode only.

### Security Event Logging
- Log authentication failures (invalid API keys).
- Log rate limit violations.
- Log prompt injection detection triggers.
- Log sandbox execution attempts and outcomes.
