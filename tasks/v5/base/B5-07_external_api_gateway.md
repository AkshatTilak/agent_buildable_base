# B5-07: External API Gateway

> **Status:** `[x]`  
> **Owner:** `gateway`  
> **Secondary:** `common/schemas`, `frontend`  
> **Complexity:** 🟡 Medium (5 subtasks)

---

## Objective

Expose an OpenAI-compatible external API at `/v1/*` that allows programmatic access to ContAIned's models and agents via standard API keys. Support prompt/agent/model routing, streaming, embeddings, and usage tracking. API keys are managed through the Settings dashboard.

---

## Acceptance Criteria

- [x] `POST /v1/chat/completions` — OpenAI-compatible request/response format
- [x] If `model` param matches an agent `endpoint_slug` → invoke that agent
- [x] If `model` param matches a `model_id` → direct LiteLLM completion
- [x] Streaming support (`stream: true`) in OpenAI SSE format
- [x] `POST /v1/embeddings` endpoint using active embedding model
- [x] `GET /v1/models` lists all available models and agent slugs
- [x] API key validation via `Authorization: Bearer <key>` header
- [x] Per-key rate limiting (configurable requests/minute)
- [x] Usage tracking: requests count, tokens consumed per key, stored in DB
- [x] API key CRUD in Settings page (generate, copy, revoke, toggle active)
- [x] Inline code examples / documentation panel in frontend

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-07a | OpenAI-Compatible Chat Completions | [`S5-07a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-07a.md) |
| S5-07b | Embeddings & Models List Endpoints | [`S5-07b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-07b.md) |
| S5-07c | API Key Management CRUD & Middleware | [`S5-07c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-07c.md) |
| S5-07d | Per-Key Rate Limiting & Usage Tracking | [`S5-07d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-07d.md) |
| S5-07e | Frontend: API Keys Tab & Docs Panel | [`S5-07e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-07e.md) |
