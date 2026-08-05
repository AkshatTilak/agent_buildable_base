# Infrastructure & Deployment Reference (V7)

> **Source:** Updated for ContAIned V7 (Hub tenancy, fail-fast Gateway, host path persistence)
> **Last Updated:** 2026-08-04

This document details Docker images, docker-compose services, local development setup, Kafka topics, per-hub datastore bindings, and containerized deployment guidance for the ContAIned AI Platform.

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
| PostgreSQL | `postgres:16` | 5432 | `../data/postgres:/var/lib/postgresql/data` | `pg_isready -U contained -d contained_platform` |
| Qdrant | `qdrant/qdrant:latest` | 6333 | `../data/qdrant:/qdrant/storage` | HTTP `/readyz` |
| Neo4j | `neo4j:5.20.0` | 7474, 7687 | `../data/neo4j:/data`, `neo4j_logs:/logs` | `cypher-shell RETURN 1` |
| Redis | `redis:7-alpine` | 6379 | `../data/redis:/data` | `redis-cli ping` |
| Kafka | `confluentinc/cp-kafka:7.6.0` | 9092 | `kafka_data:/var/lib/kafka/data` | Broker readiness |
| Gateway | `Dockerfile.gateway` | 8000 | — | Depends on: postgres, qdrant, neo4j, redis, kafka |
| Inference | `Dockerfile.inference` | 8010 | `./models:/app/models` | GPU reservation |

### Host Path Persistence ✅

Platform data is persisted to host bind mounts under the monorepo `data/` directory. This applies to
all core stateful services:

```yaml
volumes:
  - ../data/postgres:/var/lib/postgresql/data
  - ../data/qdrant:/qdrant/storage
  - ../data/redis:/data
  - ../data/neo4j:/data
```

* The `data/` folder is created automatically on first `docker compose up`.
* To reset the platform state, stop the services and remove the relevant subdirectory, e.g.
  `rm -rf data/postgres data/qdrant data/redis data/neo4j`.
* Kafka continues to use a named Docker volume (`kafka_data`) because its logs are typically
  ephemeral in local development.
* Production deployments should replace these host paths with managed volumes, network storage, or
  managed services.

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

---

## 4. Per-Hub Datastore Bindings (V6)

> Tenancy model: [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.4.

From V6, an **ingestion hub** owns the databases that store its data via `datastore_bindings`. The consequence for infrastructure is a change of role, not of topology:

- The Qdrant, Neo4j and PostgreSQL services provided by `docker-compose.yml` are now the **platform defaults**, not the only stores. They remain the fallback used implicitly by any hub that declares no binding of a given `store_type` (surfaced read-only in the UI as a synthetic *Platform Default* binding).
- An ingestion hub may instead bind to an **external or additional instance** — a managed Qdrant Cloud cluster, a separate Neo4j Aura instance, a customer-owned Postgres — by creating a `datastore_binding` with its own `connection_uri` and credentials. Compose-provided containers are untouched by this; they simply stop being that hub's storage.
- Exactly one binding per `(hub_id, store_type)` may be `is_default`. `credentials_encrypted` is Fernet-encrypted at rest with `DATASTORE_ENCRYPTION_KEY` and is never returned by the API — responses expose a masked `connection_uri` only.

### Health checks are now two-tier ✅

| Tier | Scope | Surface |
|---|---|---|
| Platform service health | The compose containers themselves (`pg_isready`, Qdrant `/readyz`, `cypher-shell RETURN 1`, `redis-cli ping`) | `/api/health`, `docker compose ps`, `deploy.py --mode check` |
| Binding health | Each `datastore_binding` row, including external endpoints the platform does not run | `GET /hubs/{hub_id}/datastores`, persisted to `health_status` / `last_health_check` |

A green platform service therefore no longer implies a healthy hub: a binding pointing at an unreachable external cluster reports `unhealthy` on the hub's datastore view while every compose container stays green. Operational alerting should consume both.

### Docker host path reset procedure

To completely reset the local platform database and vector state:

```bash
cd infrastructure
docker compose --profile core --profile graph down
rm -rf ../data/postgres ../data/qdrant ../data/redis ../data/neo4j
docker compose --profile core --profile graph up -d
```

This removes all persisted relational, vector, cache, and graph data. The Gateway will re-bootstrap
its default API key and configured `ADMIN_EMAIL` / `TEST_USER_EMAIL` accounts on the next
startup.

### Qdrant physical collection naming ✅

Collection names are unique **per hub**, so the physical Qdrant collection is namespaced:

```text
{hub_slug}__{collection_name}      # e.g. support-kb__product_docs
```

The V6 migration renames pre-existing collections to `default__{name}`. Anyone inspecting Qdrant directly — via the REST API, `qdrant-client`, or the embedded dashboard on port `6333` — must expect the prefix; the friendly name shown in the UI is the un-prefixed suffix. `syntraflow_collections.physical_name` holds the resolved physical name and is the only value that should be passed to the vector client.

### Additional infrastructure-relevant environment variables

| Variable | Purpose |
|---|---|
| `DATASTORE_ENCRYPTION_KEY` | Fernet key protecting `datastore_bindings.credentials_encrypted`. Generate once, back up with the database; losing it makes stored binding credentials unrecoverable. |
| `SMTP_*`, `APP_PUBLIC_URL`, `INVITE_TTL_HOURS`, `PASSWORD_RESET_TTL_MINUTES` | Optional email delivery for invites/approvals; see [`deployment_guide.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/deployment/deployment_guide.md) §2. |
