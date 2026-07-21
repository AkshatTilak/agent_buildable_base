# Infrastructure & Deployment Reference (V2)

> **Source:** Updated for ContAIned V2
> **Last Updated:** 2026-07-21

This document details Docker images, docker-compose services, local development setup, Kafka topics, and containerized deployment guidance for the ContAIned AI Platform.

---

## 1. Docker Images

### Dockerfile.gateway (CPU-only) ✅
- **Base:** `python:3.11-slim`
- Install Poetry for dependency management.
- Install base + all project extras (syntraflow, guardroute, evalops).
- Install `ffmpeg` binary for media processing (required by SyntraFlow).
- Copy `common/`, `gateway/`, `projects/`, and `inference/__init__.py`.
- Expose port **8000**.

### Dockerfile.inference (GPU) ✅
- **Base:** `nvidia/cuda:12.4.1-runtime-ubuntu22.04`
- Install Python 3.11, Poetry, and system build tools.
- Install base + inference extras (includes `torch`, `transformers`, `llama-cpp-python`).
- Copy `common/` and `inference/`.
- Mount `/app/models` directory for model weights.
- Expose port **8010**.

### Frontend Container / Vite Bundle ✅
- **Base:** Node 20 / React 19 / Vite 8 / Tailwind CSS v4 (`@tailwindcss/vite`).
- Build step: `npm run build` compiles TSX and processes Tailwind directives into `frontend/dist/`.

---

## 2. docker-compose Services & Network Topology

### Core Services (Default Profile) ✅

| Service | Image | Port | Volume / Host Path | Healthcheck |
|---|---|---|---|---|
| PostgreSQL | `postgres:16` | 5432 | `pgdata:/var/lib/postgresql/data` | `pg_isready -U contained -d contained_platform` |
| Qdrant | `qdrant/qdrant:latest` | 6333 | `qdrant_data:/qdrant/storage` | HTTP `/readyz` |
| Neo4j | `neo4j:5.20.0` | 7474, 7687 | `neo4j_data:/data`, `neo4j_logs:/logs` | `cypher-shell RETURN 1` |
| Redis | `redis:7-alpine` | 6379 | `redis_data:/data` | `redis-cli ping` |
| Kafka | `confluentinc/cp-kafka:7.6.0` | 9092 | `kafka_data:/var/lib/kafka/data` | Broker readiness |
| Gateway | `Dockerfile.gateway` | 8000 | — | Depends on: postgres, qdrant, neo4j, redis, kafka |
| Inference | `Dockerfile.inference` | 8010 | `./models:/app/models` | GPU reservation |

### Kafka Topics (auto-created via `kafka-setup`) ✅
1. `syntraflow-ingestion-jobs`: Ingestion job dispatch topic for document/video processing workers.
2. `agent-eval-trigger`: Asynchronous evaluation runner trigger topic for RAGAS and DeepEval.
3. `guardroute-traces`: Live execution trace diagnostic topic for GuardRoute orchestrator.

### Telemetry & Real-Time Pub/Sub ✅
- Redis Pub/Sub channel `telemetry-system-health` streams CPU utilization, memory usage, VRAM allocation, and active agent metrics via WebSocket (`/api/telemetry/ws`) and SSE (`/api/telemetry/stream`).

---

## 3. Admin & Observability Profiles

### Admin Profile (`--profile admin`) ✅
- **pgAdmin:** `dpage/pgadmin4` on port `5050`
- **Kafka UI:** `provectuslabs/kafka-ui:latest` on port `8080`

### Observability Profile (`--profile observability`) ✅
- **Jaeger:** `jaegertracing/all-in-one:latest` on port `16686` (UI) and `4317` (OTel gRPC)
- **OTEL Collector:** `otel/opentelemetry-collector:latest` on ports `4317`, `4318`
