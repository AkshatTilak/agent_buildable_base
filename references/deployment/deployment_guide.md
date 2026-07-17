# Setup & Deployment Guide: ContAIned AI Platform

This guide outlines how to build, run, and deploy the ContAIned AI Platform. It covers both **local development (hybrid native/Docker)** and **all-in-one containerized deployment**.

---

## 1. System Overview

The platform consists of the following primary components:

| Service | Environment / Runtime | External Port | Internal Address | Description |
|---|---|---|---|---|
| **PostgreSQL** | Docker (`postgres:16`) | `5432` | `postgres:5432` | Relational database (Relational metadata, schemas, API keys, etc.) |
| **Qdrant** | Docker (`qdrant/qdrant`) | `6333` | `qdrant:6333` | Vector database (Document chunks, embeddings) |
| **Neo4j** | Docker (`neo4j:5.20.0`) | `7474`, `7687` | `neo4j:7687` | Graph database (Knowledge Graph for hybrid RAG) |
| **Redis** | Docker (`redis:7-alpine`) | `6379` | `redis:6379` | Cache, session store, and rate limiter |
| **Kafka** | Docker (`cp-kafka:7.6.0`) | `9092` | `kafka:9092` | Message broker (Ingestion jobs, orchestration trace logging) |
| **Gateway** | FastAPI (CPU-only) | `8000` | `gateway:8000` | Gateway router proxying models and managing submodules |
| **Inference Server**| FastAPI (GPU / CUDA) | `8010` | `inference:8010` | Heavyweight server running local models (SenseVoice, Baidu OCR, etc.) |
| **Frontend** | React / Vite | `5173` | — | Developer Dashboard interface |

---

## 2. Configuration (`.env`)

Before running the application, make sure the `.env` file in the root directory contains your configurations. A pre-seeded template exists in [`.env.example`](file:///c:/Akshat/ContAIned/.env.example). 

### Key Env Variables:
* `ACTIVE_PROJECTS`: Comma-separated list of active modules, e.g., `["syntraflow", "guardroute", "evalops"]`.
* `GOOGLE_API_KEY` / `OPENROUTER_API_KEY`: API credentials for fallback models (e.g. Gemini).
* `CLASSIFIER_MODEL_PATH`: Location of model weights on disk (e.g. `models/Arch-Router-1.5B-Q8_0.gguf`).
* `OCR_PROVIDER`: Either `local` (to run the CPU/GPU local OCR model) or `api` (to fallback to Google's API).

---

## 3. Option A: Local Development (Hybrid Native Setup)

This is the recommended workflow for developing and debugging. It runs databases in Docker containers (so you don't need to install them locally) while running python code and the frontend natively for fast, hot-reloading iteration.

### Step 1: Spin up Databases in Docker
Run only the supporting data stores and message brokers in the background:
```bash
docker compose -f infrastructure/docker-compose.yml up postgres qdrant neo4j redis zookeeper kafka kafka-setup -d
```

### Step 2: Set up Local Python Virtual Environment
Install [Poetry](https://python-poetry.org/) if you haven't already, then install the dependencies with all extras:
```bash
# From workspace root
poetry install --all-extras
```
This installs core packages, shared library dependencies, and optional project extras.

### Step 3: Run the API Gateway Natively
Start the CPU API Gateway. It will automatically detect the database instances on localhost, run database migrations via Alembic, seed the database with a default dev API key (`sk_live_default_key`), and load the active project submodules:
```bash
poetry run uvicorn gateway.main:app --host 0.0.0.0 --port 8000 --reload
```

### Step 4: Run the Inference Server Natively
Make sure you have model weights placed under `models/` (or that model registry is configured to download them on demand). Start the inference server:
```bash
poetry run uvicorn inference.main:app --host 0.0.0.0 --port 8010 --reload
```

### Step 5: Start the React Frontend
Navigate to the `frontend/` directory, install packages, and spin up the Vite development server:
```bash
cd frontend
npm install
npm run dev
```
The frontend dashboard will be available at [http://localhost:5173](http://localhost:5173).

---

## 4. Option B: All-in-One Containerized Deployment

To simulate production environments or run the entire stack including gateway and inference inside Docker:

### System Requirements
* **Docker Engine** (or Docker Desktop)
* **NVIDIA Container Toolkit** (Required to map host GPU inside the GPU inference container)

### Step 1: Start All Services
Simply run:
```bash
docker compose -f infrastructure/docker-compose.yml up --build -d
```
Docker Compose will build custom images using:
* [`infrastructure/Dockerfile.gateway`](file:///c:/Akshat/ContAIned/infrastructure/Dockerfile.gateway) (Multi-stage, CPU-only)
* [`infrastructure/Dockerfile.inference`](file:///c:/Akshat/ContAIned/infrastructure/Dockerfile.inference) (Multi-stage, GPU/CUDA)
* And start all backend services on the bridged network `contained_net`.

### Step 2: Verify Service Status
To view status of running services:
```bash
docker compose -f infrastructure/docker-compose.yml ps
```
To view logs:
```bash
docker compose -f infrastructure/docker-compose.yml logs -f
```

---

## 5. Development Utilities & Observability

You can spin up optional monitoring and database admin UIs by specifying docker-compose profiles.

### Admin Dashboards Profile
Start `pgAdmin` (PostgreSQL client) and `Kafka UI`:
```bash
docker compose -f infrastructure/docker-compose.yml --profile admin up -d
```
* **pgAdmin:** [http://localhost:5050](http://localhost:5050) (Login: `admin@local.dev` / `admin`)
* **Kafka UI:** [http://localhost:8080](http://localhost:8080)

### Observability Profile
Start `Jaeger` tracing and the `OTel Collector` endpoint:
```bash
docker compose -f infrastructure/docker-compose.yml --profile observability up -d
```
* **Jaeger Tracing UI:** [http://localhost:16686](http://localhost:16686)

---

## 6. Accessing & Authenticating with the API

The gateway validates API requests via a Bearer token or Custom Header. A default dev API key is automatically seeded on startup if the API keys table is empty.

To query endpoints, pass the authorization header:
```bash
curl -H "Authorization: Bearer sk_live_default_key" http://localhost:8000/api/v1/health
```

---

## 7. Production Deployment Checklist

If preparing to deploy to production (e.g. GCP, AWS, or Azure):
1. **Model Weight Volume Mounts:** Model weights (`/app/models`) must be mounted to persistent storage (e.g. Google Cloud Filestore, AWS EFS, or Kubernetes Persistent Volumes) to prevent re-downloading weights on container restarts.
2. **Kubernetes Deployment:**
   - Run stateless Gateway replicas in a `Deployment` behind an Ingress controller.
   - Run Ingestion Workers as standalone deployments, configured to scale horizontally based on the consumer lag of the `syntraflow-ingestion-jobs` Kafka topic.
   - Run the Inference Server on GPU-enabled nodes (GCP GCE `L4`/`T4` instances) with autoscaling linked to GPU utilization thresholds.
   - Deploy relational/graph databases (`Postgres`, `Neo4j`, `Qdrant`) as `StatefulSets` with PVCs.
3. **CI/CD Pipeline:** Configure pipeline runners to:
   - Lint python code (`ruff`, `mypy`).
   - Run unit tests (`pytest`).
   - Run EvalOps evaluation checks (`DeepEval` / `Ragas` benchmarks) against staging.
   - Build optimization layers, tag Docker images, and push them to your private registry (e.g. GCR/GAR).
