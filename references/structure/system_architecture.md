# System Architecture & Core Infrastructure

> **Source:** Migrated from `requirements/system.md`
> **Last Updated:** 2026-08-04 (V7 fail-fast error policy + host path persistence)

This document captures the system-level architecture, environment configurations, database connections, and message queue guidelines that unify the ContAIned AI Platform monorepo.

---

## 1. Monorepo Integration Rules

### Strict Process Separation ✅
- The API Gateway and all project submodules run on **CPU-only**.
- The Inference Server loads all deep learning weights.
- Submodules must **never** import or call `torch`/`transformers` models directly; all execution must go through the typed `InferenceClient`.

### Dynamic Plug-and-Play Lifecycles ✅
- The Gateway discovers active modules via the `ACTIVE_PROJECTS` array in the environment.
- Gateway lifespan setup hooks (`setup.py` files) trigger collection checks and database setups at start time.
- If a project directory is missing or has unmet dependencies, log a warning and skip — do not crash the gateway.

### Shared Database Namespacing ✅
- All submodules share the same PostgreSQL database, Neo4j instance, and Qdrant cluster.
- SQL tables must use submodule prefixes: `syntraflow_`, `guardroute_`, `evalops_`, `model_registry_`.
- Neo4j node labels must be namespaced with project prefixes: `SyntraFlow_Entity`, `SyntraFlow_RELATION`.
- **V6:** prefixing is *physical* namespacing only. Logical isolation is provided by `hub_id` on every
  domain table — see §2.

### Model Configuration via Registry ✅
- All model references must be resolved through the Model Registry (see `references/logic/model_registry.md`).
- Submodules must not hardcode model names, dimensions, or provider strings.

---

## 2. V6 Hub Tenancy

> **Canonical reference:** [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md).
> This section is a summary for system-level orientation. Where it disagrees with `hubs.md`, `hubs.md` wins.

Through V5 the platform had a single implied global workspace, a single editable workflow, and a single
global RBAC triple (`admin` / `editor` / `viewer`). **None of that holds in V6.** A **Hub** is the unit
of tenancy: a typed, isolated workspace owning a set of resources and a set of members. There are four
immutable hub types — `ingestion`, `agent`, `workflow`, `eval` — and a deployment may hold many hubs of
each type. Every domain resource belongs to exactly one hub.

### 2.1 Tenancy Tables

| Table | Purpose |
|---|---|
| `hubs` | The tenancy record: `slug` (unique per `hub_type`), `name`, `hub_type`, `accent`, `icon`, `owner_id`, `is_archived` |
| `hub_members` | `(hub_id, user_id)` with `hub_role` ∈ `owner` \| `maintainer` \| `contributor` \| `viewer` |
| `hub_links` | Directional cross-hub consumption grants `(source_hub_id → target_hub_id, access_level)`. Hubs are isolated by default; links are **not** transitive |
| `datastore_bindings` | Physical Qdrant / Neo4j / Postgres / OpenSearch instances bound to an ingestion hub, with Fernet-encrypted credentials |
| `audit_log` | One row per mutating hub-scoped or admin action: actor, hub, resource, before/after, IP |

`hub_id` is added **NOT NULL** to every domain table (`syntraflow_collections`, `syntraflow_documents`,
`syntraflow_chunks`, `syntraflow_video_segments`, `syntraflow_jobs`, `agent_definitions`,
`agent_invocation_log`, `workflows`, `workflow_runs`, `eval_test_suites`, `eval_run_history`,
`eval_flow_traces`) and nullable on the platform-optional tables (`api_keys`, `mcp_servers`,
`playground_sessions`, where `null` means platform-wide).

### 2.2 Roles

Authorisation splits into two independent axes:

* **Platform role** (`users.platform_role`) — `admin` or `member` only. This **replaces** `users.role`;
  the V5 `editor` and `viewer` platform roles no longer exist (`editor → member`, `viewer → member`).
* **Hub role** (`hub_members.hub_role`) — `owner (4) > maintainer (3) > contributor (2) > viewer (1)`.
  A platform `admin` short-circuits every hub check and is treated as `owner`.

### 2.3 Mandatory Query Scoping

Every repository or service function touching a hub-scoped table **must** accept `hub_id` as a required
argument and include it in the `WHERE` clause. A resource id alone is never sufficient — querying by
primary key alone is an IDOR vulnerability and is rejected in review and by
`tests/test_hub_isolation.py`.

```python
# CORRECT
select(AgentDefinition).where(
    AgentDefinition.id == agent_id,
    AgentDefinition.hub_id == ctx.hub.id,
)

# FORBIDDEN
select(AgentDefinition).where(AgentDefinition.id == agent_id)
```

Resolution is enforced at the edge by `require_hub(hub_type=…, min_role=…)` in
`gateway/auth/hub_context.py`, and across hubs by `common/services/hub_resolver.py`, which re-checks
link validity **at execution time**, not only at save time. A hub the caller cannot see returns `404`,
never `403`, to prevent enumeration.

### 2.4 Route Shape Rule

**No hub-scoped resource may be reachable from a flat top-level route.** Everything nests under
`/hubs/{hub_id}/...`:

```text
/hubs/{hub_id}/collections   /hubs/{hub_id}/documents   /hubs/{hub_id}/datastores
/hubs/{hub_id}/agents        /hubs/{hub_id}/workflows   /hubs/{hub_id}/eval/suites
/hubs/{hub_id}/members       /hubs/{hub_id}/links
```

The V5 flat routes (`/agents`, `/workflows`, `/api/syntraflow/collections`, `/api/evalops/*`) are
**deleted** — no aliases, no redirects, no back-compat shims. Only genuinely platform-level surfaces
stay flat: `/admin/*`, `/models`, `/mcp` (the **MCP Registry**, renamed from the V5 "MCP Hub"),
`/infrastructure`, `/playground`, `/settings`, `/telemetry`.

### 2.5 Migration Ordering

The V6 cutover is a two-stage Alembic sequence, and both stages must be **reversible**
(`downgrade()` implemented) and **idempotent** when re-run against an already-migrated database.

1. **Stage 1 — additive, non-breaking.** Create `hubs`, `hub_members`, `hub_links`,
   `datastore_bindings`, `audit_log`, `user_invites`, `user_identities`, `workflow_versions`,
   `workflow_runs`. Add `hub_id` to every domain table as **nullable**, and add `users.platform_role`
   alongside the still-present `users.role`. Nothing is dropped and no constraint is tightened, so a V5
   application keeps running against a stage-1 database.
2. **Stage 2 — seed, backfill, constrain, drop.** Seed the four default hubs
   (`ingestion/default`, `agent/default`, `workflow/default`, `eval/default`) owned by the first admin
   (or a synthetic `system@contained.local` admin); backfill every existing row to its matching seed
   hub; `ALTER ... SET NOT NULL`; seed `hub_links` between the default hubs along every allowed
   direction so existing agent→collection, workflow→agent and eval→agent references keep working; add
   every active user to all four seed hubs (`admin → owner`, `editor → contributor`,
   `viewer → viewer`); rebuild uniqueness as `(hub_id, …)`; rename physical Qdrant collections to
   `default__{name}`; backfill and drop `users.role`, `users.is_active` and
   `syntraflow_collections.tenant_id`.

`scripts/migration_dryrun.py` restores a representative V5 dump into a throwaway database and runs the
full upgrade/downgrade cycle; `scripts/verify_v6_cutover.py` fails CI if any removed symbol, route or
column string survives outside `tasks/` and `references/`.

---

## 3. Gateway Fail-Fast Error Policy

The Gateway uses a structured, machine-readable error format for every failure path. All custom
exceptions inherit from `common.observability.exceptions.ContAInedException`, carry a stable
`error_code`, and are converted to JSON by the global exception handlers registered in
`gateway/main:app`.

### Error response schema

```json
{
  "error_code": "ENTITY_NOT_FOUND",
  "message": "Human-readable explanation",
  "details": {},
  "trace_id": "optional-opentelemetry-trace-id"
}
```

### Exception hierarchy

| Exception | error_code | Typical HTTP status |
|---|---|---|
| `EntityNotFoundException` | `ENTITY_NOT_FOUND` | 404 |
| `ValidationErrorException` | `VALIDATION_ERROR` | 422 |
| `ExternalServiceException` | `EXTERNAL_SERVICE_ERROR` | 502/503 |
| `DatabaseException` | `DATABASE_ERROR` | 500 |
| `PasswordPolicyError` | `PASSWORD_POLICY_ERROR` | 422 |
| `AccountLockedError` | `ACCOUNT_LOCKED` | 403 |

### Handler behaviour

* `ContAInedException` → JSON response with the exception's `status_code` and `error_code`.
* `HTTPException` (FastAPI/Starlette) → JSON response; `404` maps to `NOT_FOUND`, everything else to
  `HTTP_ERROR`.
* `RequestValidationError` (Pydantic) → `400` with `error_code: VALIDATION_ERROR`.
* Unhandled `Exception` → `500` with `error_code: INTERNAL_SERVER_ERROR`; stack traces are logged,
  never returned to the client.

### Fail-fast principles

1. **Never silently swallow errors.** Every repository failure, external timeout, and validation
   violation raises a typed exception or returns a structured `HTTPException`.
2. **Fail with a code.** Callers receive a stable `error_code` (e.g. `WORKFLOW_NOT_FOUND`,
   `IMPORT_UNRESOLVED_REFERENCES`) so the frontend and CLI can branch without parsing messages.
3. **Centralise handling.** `common.observability.middleware.register_exception_handlers(app)` is
   called once in `gateway/main.py`; individual routes do not write ad-hoc error envelopes.
4. **Preserve observability.** Every handled exception is logged with its `trace_id` and the
   unhandled-exception handler records the full stack trace.

---

## 4. Environment Configuration Specifications

Central `.env` file at the monorepo root containing:

### Server & Environment Setup ✅
```env
APP_NAME="ContAIned AI Platform"
APP_ENV="development"                    # development/production/testing
APP_HOST="0.0.0.0"
APP_PORT=8000                            # Gateway port
INFERENCE_SERVER_URL="http://localhost:8010"
INFERENCE_SERVER_PORT=8010
ACTIVE_PROJECTS=["syntraflow", "guardroute", "evalops"]
LOG_LEVEL="INFO"                         # DEBUG, INFO, WARNING, ERROR
```

### External AI Provider APIs ✅
```env
GOOGLE_API_KEY="your-gemini-key"         # Primary completion model — Gemini
OPENROUTER_API_KEY="your-openrouter-key" # Aggregator fallback tier
GROQ_API_KEY="your-groq-key"            # Fast inference fallback — Groq LPU
CEREBRAS_API_KEY="your-cerebras-key"     # High-volume fallback — Cerebras
OPENAI_API_KEY="your-openai-key"         # Used by DeepEval QA tests
```

### Storage Connections ✅
```env
DATABASE_URL="postgresql+asyncpg://postgres:postgres@localhost:5432/contained"
QDRANT_URL="http://localhost:6333"
QDRANT_API_KEY=""                        # Optional security token
NEO4J_URL="bolt://localhost:7687"
NEO4J_USER="neo4j"
NEO4J_PASSWORD="password"
REDIS_URL="redis://localhost:6379"       # Cache, sessions, lightweight pub/sub
```

> **V6 — these are now defaults, not the only option.** An ingestion hub may declare its own
> `datastore_bindings` (Qdrant / Neo4j / Postgres / OpenSearch) with per-hub connection URIs and
> credentials. The platform values above are used only when a hub declares no binding of that
> `store_type`, in which case a read-only synthetic "Platform Default" binding is surfaced in the UI.
> Per-hub credentials are Fernet-encrypted at rest with `DATASTORE_ENCRYPTION_KEY` and are never
> serialised into an API response — responses expose a masked `connection_uri` only.

### Model Selection Overrides ✅
```env
OCR_MODEL="glm"                          # See references/logic/model_registry.md
ASR_MODEL="sensevoice"
EMBEDDING_MODEL="jina"
CLASSIFIER_MODEL="arch"
COMPLETION_MODEL="gemini"
OCR_PROVIDER="api"                       # Legacy compat: local | api
```

### Inference Server Configurations ✅
```env
VRAM_BUDGET_MB=20000                     # Max VRAM budget in MB
CLASSIFIER_IDLE_TIMEOUT=300              # Model VRAM eviction timeout (seconds)
DEVICE="auto"                            # auto | cuda | cpu
```

### HuggingFace Hub ✅
```env
HF_HOME="~/.cache/huggingface/hub"
HF_HUB_ENABLE_HF_TRANSFER=1
HF_HUB_OFFLINE=0                        # Set to 1 for air-gapped deployments
HF_TOKEN=""                              # Token for gated model access
MODEL_CACHE_DIR="./models"               # Custom cache for GGUF/non-HF files
```

### Security ✅
```env
AUTH_ENABLED=false
CORS_ORIGINS=["http://localhost:3000","http://localhost:5173"]
```

### Email, Onboarding & Tenancy (V6) ✅

Consumed by `gateway/services/mailer.py`, `gateway/auth/invites.py` and `gateway/api/hubs.py`.
Defined in `common/config/settings.py` and templated in `.env.example`.

| Variable | Default | Purpose |
|---|---|---|
| `SMTP_HOST` | *(empty)* | SMTP server for invite / approval / reset mail. Empty ⇒ `NullMailer` |
| `SMTP_PORT` | `587` | SMTP port |
| `SMTP_USER` | *(empty)* | SMTP username |
| `SMTP_PASSWORD` | *(empty)* | SMTP password — never logged |
| `SMTP_FROM` | `ContAIned <no-reply@contained.local>` | From header on all platform mail |
| `SMTP_USE_TLS` | `true` | STARTTLS / SSL negotiation |
| `INVITE_TTL_HOURS` | `72` | Lifetime of an admin-issued invite token |
| `PASSWORD_RESET_TTL_MINUTES` | `60` | Lifetime of a password-reset token |
| `APP_PUBLIC_URL` | `http://localhost:5173` | Public base URL used to build invite / reset links |
| `ALLOW_SELF_REGISTRATION` | `true` | Whether `/auth/register` is open; self-registrants land `pending` |
| `ALLOW_MEMBER_HUB_CREATION` | `true` | Whether platform `member`s may create hubs, or only `admin`s |
| `AUTO_APPROVE_EMAIL_DOMAINS` | *(empty)* | Optional CSV (e.g. `acme.com`) whose sign-ups skip the approval gate |
| `DATASTORE_ENCRYPTION_KEY` | *(required in prod)* | Fernet key encrypting datastore-binding credentials and invite/reset material |

```env
SMTP_HOST=
SMTP_PORT=587
SMTP_USER=
SMTP_PASSWORD=
SMTP_FROM="ContAIned <no-reply@contained.local>"
SMTP_USE_TLS=true
INVITE_TTL_HOURS=72
PASSWORD_RESET_TTL_MINUTES=60
APP_PUBLIC_URL="http://localhost:5173"
ALLOW_SELF_REGISTRATION=true
ALLOW_MEMBER_HUB_CREATION=true
AUTO_APPROVE_EMAIL_DOMAINS=
DATASTORE_ENCRYPTION_KEY=
```

**Graceful fallback:** if SMTP is unconfigured or a send fails, invite creation still succeeds and
returns `{"delivered": false, "invite_url": "…"}`. The Admin Console then surfaces a **Copy invite
link** control with a warning banner. This is the only path by which a raw token is returned, and it is
returned only to the creating admin, only once.

### Bootstrapped Accounts (V7) ✅

`gateway/core/setup.py` creates the following accounts automatically on startup when
`AUTH_ENABLED=true` and the email does not already exist:

| Variable | Default | Purpose |
|---|---|---|
| `SUPER_ADMIN_EMAIL` | `admin@contained.ai` | Platform super admin; `platform_role=admin`, `status=active` |
| `SUPER_ADMIN_PASSWORD` | `AdminPass123!` | Initial password for the super admin (must be changed in production) |
| `TEST_USER_EMAIL` | `testuser@contained.ai` | Optional automated test account; `platform_role=member`, `status=active` |
| `TEST_USER_PASSWORD` | `TestPass123!` | Initial password for the test user |

Both accounts are created with a local `password` identity in `user_identities`.

### Observability & Tracing ✅
```env
OTEL_SERVICE_NAME="contained-platform"
OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4317"
LANGSMITH_TRACING=false
LANGSMITH_API_KEY=""
LANGSMITH_PROJECT="contained-platform"
```

---

## 5. Host Path Persistence

Local development and lightweight deployments use **host bind mounts** instead of named Docker
volumes. This keeps platform state visible on the host filesystem under the monorepo `data/`
directory and makes backups, inspection, and reset operations straightforward.

### Bind mount table

| Service | Host path | Container path | Purpose |
|---|---|---|---|
| PostgreSQL | `./data/postgres` | `/var/lib/postgresql/data` | Relational database files |
| Qdrant | `./data/qdrant` | `/qdrant/storage` | Vector store collections and snapshots |
| Redis | `./data/redis` | `/data` | AOF persistence and cache state |
| Neo4j | `./data/neo4j` | `/data` | Graph database store and transactions |

### Operational notes

* The `data/` directory is created automatically by Docker Compose on first start.
* To perform a **factory reset**, stop the containers and delete the relevant host path, e.g.
  `rm -rf data/postgres data/qdrant data/redis data/neo4j`.
* Do not commit the `data/` directory to version control; it is excluded by `.gitignore`.
* In production, replace the host bind mounts with managed volumes or network storage appropriate
  for the orchestrator (e.g. Kubernetes PVCs, EBS, managed Postgres/Qdrant/Neo4j services).

---

## 6. Database & Message Queue Setup

### PostgreSQL (Relational) ✅
- **Async engine:** `create_async_engine` with `asyncpg` driver.
- **Migrations:** Alembic (`alembic.ini` + `migrations/` at monorepo root). Each submodule maintains migration revisions with prefixed IDs. The V6 hub-tenancy cutover is the two-stage sequence described in §2.5.
- **Connection pool:** `pool_size=10`, `max_overflow=20`, `pool_pre_ping=True`.
- **Startup:** Retry/backoff if database is not yet ready.
- **V6 scoping:** every domain table carries `hub_id`; all queries filter on it (§2.3).

### Qdrant (Vector RAG - Multi-Collection) ✅
- **Dynamic Collections:** Collections are created dynamically per hub instead of a single `syntraflow_chunks_v1` collection. The **physical** collection name is `{hub_slug}__{collection_name}`, preserving global uniqueness in the vector store while allowing friendly per-hub names (`syntraflow_collections.name` is unique per `(hub_id, name)`).
- **Per-hub instances:** an ingestion hub may point at its own Qdrant via a `datastore_binding`; `QDRANT_URL` is the fallback default.
- **Vector dimension:** Dynamically determined by active embedding model:
  - jina-clip-v2: **1024**
  - nomic-embed-vision-v2: **768**
  - Gemini Embedding 2: configurable up to **3072**
- Distance metric: **Cosine**.
- On startup, validate connectivity and initialize the `syntraflow_collections` tracking table in Postgres.

### Neo4j (GraphRAG) ✅
- Shared client in `common/clients/neo4j.py`.
- **Read-only protections:** All Cypher queries block write commands (`CREATE`, `MERGE`, `DELETE`, `SET`, `REMOVE`, `DROP`).
- Transaction sessions closed cleanly after each read-write batch.
- Parameterized Cypher queries exclusively — no string interpolation.
- **Per-hub instances:** an ingestion hub may bind its own Neo4j via a `datastore_binding`; `NEO4J_URL` is the fallback default. Graph reads and writes are additionally filtered by the hub's node namespace.

### Redis (Cache & Sessions) ✅
- Instance at `localhost:6379`.
- Uses: session storage (GuardRoute), response caching, rate limit state.
- AOF persistence enabled.

### Kafka (Async Messaging) ✅
- Broker at `localhost:9092`.
- Kafka + ZooKeeper (or KRaft) services in `docker-compose.yml`.
- Topics:
  - `syntraflow-ingestion-jobs` — Ingestion queue
  - `guardroute-traces` — Orchestration spans
- `confluent-kafka` dependency in `guardroute` and `evalops` extras.

---

## 7. Embedded UI Proxies (Unified Dashboarding)
To leverage existing infrastructure without rebuilding management screens:
- **API Gateway Reverse Proxy:** The FastAPI Gateway serves as an authenticated reverse proxy for internal infrastructure dashboards.
- **Qdrant UI:** Exposed from internal port `6333` to the frontend via Gateway routing.
- **Neo4j Browser:** Exposed from internal port `7474`.
- **Frontend Integration:** React frontend uses iframes pointing to these authenticated Gateway proxy endpoints, ensuring RBAC is enforced before granting access to infrastructure UIs.
- **V6 access:** `/infrastructure` is a platform-level surface restricted to platform `admin`s. Per-hub datastore health is surfaced separately inside each ingestion hub's **Datastores** tab.

---

## 8. Startup & Shutdown Lifecycle

### Startup Sequence (Pending Implementation)
1. Load `.env` and validate required settings (fail-fast on missing critical keys).
2. Initialize database connections (Postgres, Qdrant, Neo4j, Redis) with retry/backoff.
3. Run Alembic migrations (if `APP_ENV != testing`).
4. Load Model Registry from database; apply environment overrides.
5. Resolve hub datastore bindings; fall back to the platform defaults for any `(hub, store_type)` with no binding.
6. Iterate `ACTIVE_PROJECTS`: import `setup.py` hooks, call `init_app_state()`.
7. Mount project API routers dynamically under the `/hubs/{hub_id}` prefix.
8. Start OpenTelemetry instrumentation.
9. Start serving requests.

### Shutdown Sequence (Pending Implementation)
1. Stop accepting new requests (graceful drain with configurable timeout).
2. Call `shutdown_app_state()` for each active project.
3. Close inference client connections.
4. Commit/flush Kafka producer buffers.
5. Close database connections (Postgres, Neo4j, Redis sessions).
6. Log shutdown confirmation.

### Graceful Degradation (Pending Implementation)
- **Neo4j unavailable:** Disable GraphRAG features; log warning; vector-only retrieval.
- **Kafka unavailable:** Fall back to local file logging for traces.
- **Inference Server unavailable:** Return `503` for model endpoints; cloud-only models still work.
- **Redis unavailable:** Disable caching and rate limiting; log warning.
