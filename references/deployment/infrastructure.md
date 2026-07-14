# Infrastructure & Deployment

> **Source:** Migrated from `requirements/infrastructure.md`
> **Last Updated:** 2026-07-14

This document details Docker images, docker-compose services, local development setup, and production deployment guidance for the ContAIned AI Platform.

---

## 1. Docker Images

### Dockerfile.gateway (CPU-only) ✅
- **Base:** `python:3.11-slim`
- Install Poetry for dependency management.
- Install base + all project extras (syntraflow, guardroute, evalops).
- Install `ffmpeg` binary for media processing (required by SyntraFlow).
- Copy `common/`, `gateway/`, `projects/`, and `inference/__init__.py`.
- Do **NOT** install GPU packages (`torch`, `transformers`, `paddleocr`).
- Expose port **8000**.

### Dockerfile.inference (GPU) ✅
- **Base:** `nvidia/cuda:12.4.1-runtime-ubuntu22.04`
- Install Python 3.11, Poetry, and system build tools.
- Install base + inference extras (includes `torch`, `transformers`, `llama-cpp-python`).
- Install framework-specific packages based on configured models (`funasr`, `paddleocr`, `surya-ocr`, etc.).
- Copy `common/` and `inference/`.
- Create `/app/models` directory for weight storage (mount as volume at runtime).
- Expose port **8010**.

### Image Security & Optimization (Partial)
- [ ] Use multi-stage builds to reduce final image size.
- [ ] Pin base image versions (avoid `latest` tags in production).
- [ ] Run as non-root user.
- [x] Add `.dockerignore` to exclude `__pycache__`, `.git`, `.env`, `node_modules`.

---

## 2. docker-compose Services

### Critical Bug Fix ✅
The inference service had a duplicated `resources` key. Fixed:
```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: 1
          capabilities: [gpu]
```

### Core Services (Default Profile) ✅

| Service | Image | Port | Volume | Healthcheck |
|---|---|---|---|---|
| PostgreSQL | `postgres:16` | 5432 | `pgdata:/var/lib/postgresql/data` | `pg_isready -U contained -d contained_platform` |
| Qdrant | `qdrant/qdrant:latest` | 6333 | `qdrant_data:/qdrant/storage` | HTTP `/readyz` |
| Neo4j | `neo4j:5.20.0` | 7474, 7687 | `neo4j_data:/data`, `neo4j_logs:/logs` | `cypher-shell RETURN 1` |
| Redis | `redis:7-alpine` | 6379 | `redis_data:/data` | `redis-cli ping` |
| Kafka | `confluentinc/cp-kafka:7.6.0` | 9092 | `kafka_data:/var/lib/kafka/data` | Broker readiness |
| Gateway | `Dockerfile.gateway` | 8000 | — | Depends on: postgres, qdrant, neo4j, redis |
| Inference | `Dockerfile.inference` | 8010 | `./models:/app/models` | GPU reservation |

**Kafka Topics (auto-created):** `syntraflow-ingestion-jobs`, `guardroute-traces`

### Admin Profile (`--profile admin`) ✅

| Service | Image | Port |
|---|---|---|
| pgAdmin | `dpage/pgadmin4` | 5050 |
| Kafka UI | `provectuslabs/kafka-ui:latest` | 8080 |

### Observability Profile (`--profile observability`) ✅

| Service | Image | Ports |
|---|---|---|
| Jaeger | `jaegertracing/all-in-one:latest` | 16686 (UI), 4317 (gRPC), 4318 (HTTP) |
| OTEL Collector | `otel/opentelemetry-collector:latest` | 4317, 4318 |

### Volume Declarations ✅
```yaml
volumes:
  pgdata:
  qdrant_data:
  neo4j_data:
  neo4j_logs:
  redis_data:
  kafka_data:
```

---

## 3. Local Development (Without Docker)

### Minimal Setup (Pending)
Run only infrastructure services via Docker, with gateway and inference running natively:
```bash
# Start only databases
docker compose -f infrastructure/docker-compose.yml up postgres qdrant neo4j redis kafka

# Terminal 1: Start gateway
poetry install --all-extras
uvicorn gateway.main:app --host 0.0.0.0 --port 8000 --reload

# Terminal 2: Start inference
uvicorn inference.main:app --host 0.0.0.0 --port 8010 --reload
```

### Selective Service Startup (Pending)
```bash
# Just Postgres + Qdrant (no Neo4j, no Kafka)
docker compose up postgres qdrant
```
The gateway should gracefully handle missing services (log warnings, skip functionality).

### Hot Reload (Pending)
- Gateway: `--reload` flag enabled when `APP_ENV=development`.
- Inference: `--reload` flag enabled when `APP_ENV=development`.
- Frontend (if Vite): `npm run dev` with HMR.

---

## 4. Production Deployment Considerations (Future)

### Cloud Provider
- Recommended: **Google Cloud Platform (GCP)** for proximity to Gemini API.
- GPU instances: GCE with NVIDIA T4/L4 for inference.
- Alternative: Any provider with NVIDIA GPU support and Docker/Kubernetes.

### Container Orchestration
- Kubernetes manifests or Helm charts (future).
- Cloud Run for stateless gateway (future).
- Persistent volumes for model weights.

### CI/CD Pipeline
- GitHub Actions workflow for: Linting (`ruff`, `mypy`), Unit tests (`pytest`), EvalOps safety tests on PR, Docker image builds, Optional deploy to staging on merge to main.
