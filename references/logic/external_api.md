# External API Gateway Architecture

> **Source:** V5 Planning  
> **Last Updated:** 2026-07-23

---

## 1. Overview

OpenAI-compatible API at `/v1/*` for programmatic access. Supports chat completions, embeddings, and model listing. Authenticated via API keys (`sk-` prefix). Rate-limited per key.

> **V6 UPDATE:** agents are hub-scoped, so an agent is addressed by the **hub-qualified** identifier
> `"{hub_slug}/{agent_slug}"`. The bare-slug form used in V5 is **removed**. API keys may themselves be
> hub-scoped (§5). See
> [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md).

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

# Use an agent by its hub-qualified identifier: "{hub_slug}/{agent_slug}"
response = client.chat.completions.create(
    model="research/my-research-agent",
    messages=[{"role": "user", "content": "Search papers on transformers"}]
)
```

```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Authorization: Bearer sk-your-key-here" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "research/my-research-agent",
        "messages": [{"role": "user", "content": "Search papers on transformers"}]
      }'

curl http://localhost:8000/v1/models \
  -H "Authorization: Bearer sk-your-key-here"
```

Because `endpoint_slug` is only unique per hub, the bare form `"my-research-agent"` is ambiguous and is
rejected with `404` and a message pointing at the hub-qualified form.

---

## 3. Routing Logic

When `/v1/chat/completions` receives a `model` parameter:
1. **Hub-qualified agent match**: if `model` is of the form `"{hub_slug}/{agent_slug}"` and that hub
   contains an agent with that `endpoint_slug` → authorise the key against the hub (§5), then invoke the
   agent with its configured model, system_prompt and tools
2. **Model ID match**: if `model` matches a `model_id` in `model_registry` → direct `completion_with_fallback()`
3. **No match**: return 404 with the list of models and hub-qualified agents this key may use

Ambiguity note: model ids such as `gemini/gemini-3.5-flash` share the `a/b` shape. Resolution checks
`model_registry` for an exact `model_id` match before, and independently of, hub/agent resolution; a
registry hit always wins, and hub slugs may not collide with provider prefixes.

Agent invocation performs the same hub-link validation as the internal route — a retrieval collection in
another hub requires a live `hub_link`, re-checked at execution time, else `HUB_LINK_REVOKED`. See
[`agent_endpoints.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/agent_endpoints.md).

---

## 4. API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/v1/chat/completions` | POST | Chat completion (OpenAI format) |
| `/v1/embeddings` | POST | Text embedding |
| `/v1/models` | GET | List available models + the hub-qualified agents this key is authorised for |

`GET /v1/models` is **authorisation-filtered**: it never lists an agent the calling key cannot invoke.
For a hub-scoped key it returns only that hub's agents; for a platform key it returns agents in every
hub the owning user is a member of (all hubs, if the owner is a platform `admin`). Agents are always
reported using the `"{hub_slug}/{agent_slug}"` identifier.

---

## 5. API Key Management

- Keys generated with `sk-` prefix + 48 random chars
- Stored hashed (SHA-256) — full key shown only once at creation
- `prefix` field stores first 8 chars for display
- Fields: id, key (hashed), name, user_id, **hub_id (nullable)**, is_active, rate_limit, usage_count, last_used_at, created_at

### Key scope (V6)

`api_keys.hub_id` is **nullable** and determines the key's blast radius:

| `hub_id` | Scope | Reach |
|---|---|---|
| `null` | **Platform-wide key** | May invoke any agent its **owning user** can reach — i.e. agents in hubs the user is a member of, or all agents if the owner is a platform `admin` |
| set | **Hub-scoped key** | May invoke **only** agents in that one hub |

- A hub-scoped key presented against another hub's agent fails as if the agent did not exist (`404`),
  never `403`, consistent with the hub enumeration policy in
  [`security.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md) §1.2.
- A platform key's reach is re-evaluated per request from live membership, so revoking a hub membership
  immediately narrows every platform key owned by that user.
- A key whose owning user is not `status = "active"` is rejected outright.
- Hub-scoped keys are managed from the hub; platform keys from `/settings/api-keys`
  (platform `admin`).

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

- `/v1/*` routes: API key auth (separate middleware), scoped per §5
- `/api/*` and `/hubs/*` routes: JWT auth (session-based; OAuth or password)
- No cross-dependency between the two auth systems
