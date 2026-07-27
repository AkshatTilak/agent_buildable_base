# System Architecture & Core Infrastructure

> **Source:** Migrated from `requirements/system.md`
> **Last Updated:** 2026-07-14

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

### Model Configuration via Registry ✅
- All model references must be resolved through the Model Registry (see `references/logic/model_registry.md`).
- Submodules must not hardcode model names, dimensions, or provider strings.

---

## 2. Environment Configuration Specifications

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

### Observability & Tracing ✅
```env
OTEL_SERVICE_NAME="contained-platform"
OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4317"
LANGSMITH_TRACING=false
LANGSMITH_API_KEY=""
LANGSMITH_PROJECT="contained-platform"
```

---

## 3. Database & Message Queue Setup

### PostgreSQL (Relational) ✅
- **Async engine:** `create_async_engine` with `asyncpg` driver.
- **Migrations:** Alembic (`alembic.ini` + `migrations/` at monorepo root). Each submodule maintains migration revisions with prefixed IDs.
- **Connection pool:** `pool_size=10`, `max_overflow=20`, `pool_pre_ping=True`.
- **Startup:** Retry/backoff if database is not yet ready.

### Qdrant (Vector RAG - Multi-Collection) ✅
- **Dynamic Collections:** Collections are created dynamically per tenant/project instead of a single `syntraflow_chunks_v1` collection.
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

## 4. Embedded UI Proxies (Unified Dashboarding)
To leverage existing infrastructure without rebuilding management screens:
- **API Gateway Reverse Proxy:** The FastAPI Gateway serves as an authenticated reverse proxy for internal infrastructure dashboards.
- **Qdrant UI:** Exposed from internal port `6333` to the frontend via Gateway routing.
- **Neo4j Browser:** Exposed from internal port `7474`.
- **Frontend Integration:** React frontend uses iframes pointing to these authenticated Gateway proxy endpoints, ensuring RBAC is enforced before granting access to infrastructure UIs.

---

## 5. Startup & Shutdown Lifecycle

### Startup Sequence (Pending Implementation)
1. Load `.env` and validate required settings (fail-fast on missing critical keys).
2. Initialize database connections (Postgres, Qdrant, Neo4j, Redis) with retry/backoff.
3. Run Alembic migrations (if `APP_ENV != testing`).
4. Load Model Registry from database; apply environment overrides.
5. Iterate `ACTIVE_PROJECTS`: import `setup.py` hooks, call `init_app_state()`.
6. Mount project API routers dynamically.
7. Start OpenTelemetry instrumentation.
8. Start serving requests.

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
