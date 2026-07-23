# External API Gateway Architecture

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

## 1. Overview

OpenAI-compatible API at `/v1/*` for programmatic access. Supports chat completions, embeddings, and model listing. Authenticated via API keys (`sk-` prefix). Rate-limited per key.

---

## 2. OpenAI SDK Compatibility

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8000/v1",
    api_key="sk-your-key-here"
)

# Use a model directly
response = client.chat.completions.create(
    model="gemini/gemini-3.5-flash",
    messages=[{"role": "user", "content": "Hello"}]
)

# Use an agent by its endpoint slug
response = client.chat.completions.create(
    model="my-research-agent",
    messages=[{"role": "user", "content": "Search papers on transformers"}]
)
```

---

## 3. Routing Logic

When `/v1/chat/completions` receives a `model` parameter:
1. **Agent slug match**: if `model` matches an `endpoint_slug` in `agent_definitions` → invoke agent with its configured model, system_prompt, tools
2. **Model ID match**: if `model` matches a `model_id` in `model_registry` → direct `completion_with_fallback()`
3. **No match**: return 404 with available models list

---

## 4. API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/v1/chat/completions` | POST | Chat completion (OpenAI format) |
| `/v1/embeddings` | POST | Text embedding |
| `/v1/models` | GET | List available models + agent slugs |

---

## 5. API Key Management

- Keys generated with `sk-` prefix + 48 random chars
- Stored hashed (SHA-256) — full key shown only once at creation
- `prefix` field stores first 8 chars for display
- Fields: id, key (hashed), name, user_id, is_active, rate_limit, usage_count, last_used_at, created_at

---

## 6. Rate Limiting

- Redis sliding window counter per API key
- Default: 60 requests/minute (configurable per key)
- Response headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`
- 429 response with `Retry-After` header when exceeded
- Graceful degradation if Redis unavailable (allow all, log warning)

---

## 7. Usage Tracking

- `api_key_usage` table: per-request logging (endpoint, model, tokens, latency, status)
- Logged asynchronously via BackgroundTask
- Stats endpoint: `GET /api/settings/api-keys/{id}/usage`

---

## 8. Auth Separation

- `/v1/*` routes: API key auth (separate middleware)
- `/api/*` routes: JWT auth (OAuth-based)
- No cross-dependency between the two auth systems
