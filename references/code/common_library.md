# Shared Library (`common/`) — Implementation Reference

> **Source:** Migrated from `requirements/common.md`
> **Last Updated:** 2026-07-14

This document details the shared library modules used by all backends and project submodules. All items below are **completed** (`✅`) and serve as reference context for downstream agents.

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

---

## 5. Dependencies Required

Additions to `pyproject.toml` base dependencies:
```toml
"redis[hiredis] (>=5.0.0,<6.0.0)",
"huggingface-hub (>=0.23.0,<1.0.0)",
"neo4j (>=5.20.0,<6.0.0)",        # moved from syntraflow extras to base
```
