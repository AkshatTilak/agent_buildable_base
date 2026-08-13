# Shared Library (`common/`) — Implementation Reference

> **Source:** Migrated from `requirements/common.md`
> **Last Updated:** 2026-07-28

This document details the shared library modules used by all backends and project submodules. All items below are **completed** (`✅`) and serve as reference context for downstream agents.

> V6 adds hub tenancy to the shared library (`common/schemas/hubs.py`, `common/services/hub_repository.py`, `common/services/hub_resolver.py`, and the tenancy models in `common/models/database.py`). The tenancy model itself is defined in [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) and is not restated here.

---

## 1. Configuration (`common/config/`) ✅

### Pydantic-Settings Composition Pattern
- Multi-inheritance composes domain-specific settings classes into a single `Settings` instance.
- All backends and projects import settings from `common.config.settings`.
- New settings classes are added as parents to the composed `Settings` when adding features.

### Settings Validation on Startup
- Fail-fast if required environment variables are missing (e.g., `DATABASE_URL`).
- Log a warning for optional but recommended variables (e.g., `GOOGLE_API_KEY`).
- Validate `ACTIVE_PROJECTS` entries against available project directories.

### Settings Fields
| Field | Type | Default | Purpose |
|---|---|---|---|
| `LOG_LEVEL` | `str` | `"INFO"` | Configurable log verbosity |
| `VRAM_BUDGET_MB` | `int` | `20000` | GPU memory budget |
| `REDIS_URL` | `str` | `"redis://localhost:6379"` | Redis connection |
| `CORS_ORIGINS` | `list[str]` | `["http://localhost:3000", "http://localhost:5173"]` | CORS allowlist |
| `AUTH_ENABLED` | `bool` | `False` | Toggle API key auth |
| `GROQ_API_KEY` | `Optional[str]` | `None` | Groq provider key |
| `CEREBRAS_API_KEY` | `Optional[str]` | `None` | Cerebras provider key |
| `HF_HOME` | `str` | `"~/.cache/huggingface/hub"` | HuggingFace cache |
| `MODEL_CACHE_DIR` | `str` | `"./models"` | Local model weights cache (HF checkpoints + GGUF) |
| `DEVICE` | `str` | `"auto"` | Device selection |
| `INFERENCE_SERVER_PORT` | `int` | `8010` | Explicit port field |

### Model Override Settings
| Field | Type | Default | Purpose |
|---|---|---|---|
| `OCR_MODEL` | `str` | `"glm"` | Active OCR model shorthand |
| `ASR_MODEL` | `str` | `"sensevoice"` | Active ASR model shorthand |
| `EMBEDDING_MODEL` | `str` | `"jina"` | Active embedding model shorthand |
| `CLASSIFIER_MODEL` | `str` | `"arch"` | Active classifier model shorthand |
| `COMPLETION_MODEL` | `str` | `"gemini"` | Active completion model shorthand |

---

## 2. Database Clients (`common/clients/`) ✅

### PostgreSQL Client (`postgres.py`)
- Uses `create_async_engine()` from `sqlalchemy.ext.asyncio` (fixed from sync).
- `AsyncSession` throughout (compatible with `asyncpg` driver).
- Connection pooling: `pool_size=10`, `max_overflow=20`, `pool_pre_ping=True`.
- `get_async_db()` FastAPI dependency generator yielding `AsyncSession`.
- Connection failures handled with retry/backoff on startup.

### Qdrant Client (`qdrant.py`)
- `VectorClient` wrapper maintained.
- Configurable timeout and retry policy.
- `async_search_similarity()` for non-blocking searches.
- Accepts vector dimension dynamically from the Model Registry.

### Neo4j Client (`neo4j.py`)
- Wraps the `neo4j` Python driver.
- Connection pooling with configurable session lifecycle management.
- **Read-only mode enforcement:** blocks Cypher commands containing `CREATE`, `MERGE`, `DELETE`, `SET`, `REMOVE`, `DROP` for read-only tools.
- Parameterized query helpers to prevent Cypher injection.
- Graceful connection failure with retry logic.
- `get_neo4j_session()` dependency generator.

### Inference Client (`inference.py`)
- Model-agnostic `transcribe()` docstring (fixed from "Whisper-v3-turbo").
- Return schema includes `emotion` and `audio_events` fields for ASR.
- Configurable timeout (default: 120s).
- Retry policy for transient failures (503, connection reset).
- Circuit breaker: after N consecutive failures, mark inference server as degraded.

### LiteLLM Client (`litellm.py`)
- Default model: `gemini/gemini-3.5-flash` (fixed from `gemini-1.5-flash`).
- Uses Model Registry's fallback configuration instead of hardcoded list.
- Groq and Cerebras added to fallback chain.
- Token counting for cost tracking.
- Context truncation logic for models with smaller context windows.

### Redis Client (`redis.py`)
- Wraps `redis[hiredis]`.
- Async Redis client for caching, session storage, and pub/sub.
- Configured from `REDIS_URL` setting.

---

## 3. Observability (`common/observability/`) ✅

### Logger (`logger.py`)
- Reads `LOG_LEVEL` from settings (fixed from hardcoded `INFO`).
- Supports structured JSON logging for production (`APP_ENV=production`).
- Request ID correlation in log entries.

### Tracing (`tracing.py`)
- `from typing import Optional` import included (fixed crash).
- OpenTelemetry OTLP export to Jaeger.
- LangSmith integration toggle for LLM-specific tracing.
- Span naming conventions: `{project}.{operation}` (e.g., `syntraflow.ingest`, `guardroute.classify`).

---

## 4. Schemas (`common/schemas/`) ✅

### Agent Types (`agent_types.py`)
- Existing schemas: `TaskComplexity`, `SubAgentStatus`, `SubAgentResult`, `ClassificationResult`.
- `SubAgentResult` includes `latency_ms: float` for timing.
- `SubAgentResult` includes `model_used: str` for model tracking.

### Model Registry Types
- `ModelSpec` — Pydantic model representing a registry entry.
- `ModelRole` — Enum: `OCR`, `ASR`, `EMBEDDING`, `CLASSIFIER`, `COMPLETION`.
- `ModelMode` — Enum: `LOCAL`, `CLOUD`, `AUTO`.

### API Request/Response Schemas
- `HealthResponse` — Standardized health check response.
- `ErrorResponse` — Standardized error response with error codes.
- `PaginatedResponse` — Standardized paginated list response.

### Hub Schemas (`hubs.py`) — V6 ✅
Pydantic contracts for the Hub tenancy model. Definitions are authoritative in
[`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md); this module is their code surface.

- `HubType` — Enum: `INGESTION`, `AGENT`, `WORKFLOW`, `EVAL`. Immutable after creation.
- `HubRole` — Ordered enum: `owner (4) > maintainer (3) > contributor (2) > viewer (1)`, with a `satisfies(min_role)` comparison helper.
- `PlatformRole` — Enum: `ADMIN`, `MEMBER` (replaces the V5 `admin/editor/viewer` triple).
- `HubCreate` / `HubUpdate` / `HubRead` — CRUD payloads; `HubRead` carries `slug`, `hub_type`, `accent`, `icon`, `is_archived`, and the caller's effective `hub_role`.
- `HubMemberCreate` / `HubMemberRead` — membership payloads.
- `HubLinkCreate` / `HubLinkRead` — cross-hub grants with `access_level` ∈ `read | use`.
- `DatastoreBindingCreate` / `DatastoreBindingRead` — `DatastoreBindingRead` **never** exposes `credentials_encrypted` and returns a masked `connection_uri`.
- `QualifiedRef` — `{type, hub_id, resource_id}` cross-hub reference used by workflow nodes, agent collection bindings, and eval targets. A bare resource id is never a valid reference.
- `AuditLogRead` — audit row projection with `before_json` / `after_json`.

---

## 5. Hub Services (`common/services/`) — V6 ✅

### Hub Repository (`hub_repository.py`)
The only sanctioned data-access path for hub-scoped tables. Every helper is **hub-scoped by construction**:

```python
async def list_hubs(session, *, user_id, hub_type=None, include_archived=False) -> list[Hub]: ...
async def get_hub(session, *, hub_id, user_id) -> Hub | None: ...
async def create_hub(session, *, hub_type, name, slug, owner_id, **fields) -> Hub: ...
async def update_hub(session, *, hub_id, **fields) -> Hub: ...
async def archive_hub(session, *, hub_id) -> Hub: ...          # archive, never hard-delete unless empty
async def list_members(session, *, hub_id) -> list[HubMember]: ...
async def effective_role(session, *, hub_id, user_id) -> str | None: ...
```

**Hard rule — no repository function may accept a resource id without also accepting `hub_id`.**
Every read path filters on `hub_id`; a primary key alone is never sufficient:

```python
# CORRECT
stmt = select(AgentDefinition).where(
    AgentDefinition.id == agent_id,
    AgentDefinition.hub_id == hub_id,
)

# FORBIDDEN — IDOR vulnerability
stmt = select(AgentDefinition).where(AgentDefinition.id == agent_id)
```

`tests/test_hub_isolation.py` asserts statically that no hub-scoped model is queried by primary key alone anywhere in `gateway/` or `projects/`.

### Hub Resolver (`hub_resolver.py`)
Resolves and authorises **cross-hub** references:

```python
async def resolve_linked(session, source_hub_id, target_resource_type, target_resource_id) -> Any:
    """Loads the target resource, then asserts a hub_link exists from source_hub_id to the
    target's hub_id with a sufficient access_level. Raises HubLinkError (403) otherwise."""
```

- **Direction matrix** — only these source → target pairings are permitted; anything else is rejected with `422`:
  `agent → ingestion`, `workflow → agent`, `workflow → ingestion`, `eval → workflow`, `eval → agent`.
  Nothing may link *into* an `eval` hub.
- **Non-transitive** — a `workflow` hub linked to an `agent` hub does **not** inherit that agent hub's `ingestion` links. Each consuming hub must declare its own link.
- **Execution-time re-validation** — link validity is re-checked before a node/agent/suite actually runs, not only at save or publish time. Revoking a link immediately fails dependent executions with `HUB_LINK_REVOKED` rather than silently reading across a tenancy boundary.
- `HubLinkError` — raised on a missing, revoked, insufficient, or mis-directed link; surfaced as `403` with a machine-readable code.

### Credential Encryption Helpers (`common/services/crypto.py`)
Used by `datastore_bindings.credentials_encrypted`:

```python
def encrypt_secret(plaintext: str) -> str: ...      # Fernet, key = DATASTORE_ENCRYPTION_KEY
def decrypt_secret(ciphertext: str) -> str: ...
def mask_uri(connection_uri: str) -> str: ...       # postgres://user:****@host:5432/db
```

- Backed by `cryptography.fernet.Fernet` with the key from `DATASTORE_ENCRYPTION_KEY`; startup fails fast if a binding exists and the key is absent or malformed.
- Decrypted credentials exist only in-process for the duration of a connection attempt. They are never serialised into an API response, a log line, an audit row, or a trace attribute.
- `mask_uri()` is applied to every `connection_uri` on the way out of the API, including health-check payloads and error messages.

---

## 6. ORM Models (`common/models/database.py`) — V6 ✅

In addition to the V5 tables, `database.py` now hosts the tenancy and user-management models:

| Model | Table | Notes |
|---|---|---|
| `Hub` | `hubs` | `UNIQUE (hub_type, slug)`; archived, not deleted |
| `HubMember` | `hub_members` | `UNIQUE (hub_id, user_id)` |
| `HubLink` | `hub_links` | directional grant, `UNIQUE (source_hub_id, target_hub_id)` |
| `DatastoreBinding` | `datastore_bindings` | `UNIQUE (hub_id, name)`; encrypted credentials |
| `AuditLog` | `audit_log` | one row per mutating hub-scoped or admin action; `hub_id` null = platform-level |
| `UserIdentity` | `user_identities` | multi-provider linking (`google` / `github` / `password`) |
| `UserInvite` | `user_invites` | hashed token, TTL, pre-assigned role + hub grants |
| `WorkflowVersion` | `workflow_versions` | immutable graph snapshots, `UNIQUE (workflow_id, version_number)` |
| `WorkflowRun` | `workflow_runs` | persisted run history with per-node traces |

### `HubScopedMixin` convention

Every domain model that belongs to a hub inherits the mixin rather than declaring the column ad hoc:

```python
class HubScopedMixin:
    @declared_attr
    def hub_id(cls) -> Mapped[str]:
        return mapped_column(
            String(36), ForeignKey("hubs.id", ondelete="CASCADE"),
            nullable=False, index=True,
        )
```

Rules the mixin encodes:
- `hub_id` is **NOT NULL** and indexed on every scoped table; child tables inherit scope through their parent FK (e.g. `eval_test_cases` via `suite_id`).
- Uniqueness is per hub, not global — `UNIQUE (hub_id, name)` for collections and workflows, `UNIQUE (hub_id, endpoint_slug)` for agents, `UNIQUE (hub_id, name)` for eval suites.
- Deleting a hub cascades to its resources; hubs are archived instead unless empty.
- Adding the mixin to a model without adding `hub_id` to the corresponding repository signature is a review-blocking error.

Nullable `hub_id` (an *optional* context tag, not a scope) applies only to `api_keys`, `mcp_servers`, and `playground_sessions` — these do not use the mixin.

---

## 7. Dependencies Required

Additions to `pyproject.toml` base dependencies:
```toml
"redis[hiredis] (>=5.0.0,<6.0.0)",
"huggingface-hub (>=0.23.0,<1.0.0)",
"neo4j (>=5.20.0,<6.0.0)",        # moved from syntraflow extras to base
"cryptography (>=42.0.0,<47.0.0)", # V6: Fernet encryption for datastore credentials
```
