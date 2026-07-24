# Setup & Deployment Guide: ContAIned AI Platform (V5)

This guide outlines how to build, run, test, and deploy the **ContAIned AI Platform V5**. It covers **automated CLI deployment**, **local hybrid native/Docker development**, **Tailwind CSS v4 frontend integration**, and **all-in-one containerized deployment**.

---

## 1. Automated Unified Deployment (Recommended)

The platform includes a cross-platform Python CLI script (`scripts/deploy.py`) to manage git submodules, `.env` validation, Docker Compose infrastructure profiles, database migrations, health diagnostics, and test suites in a single command.

### Deploy All Infrastructure & Run Diagnostics:
```bash
python scripts/deploy.py --mode all --profile core
```

### Supported Modes (`--mode`):
- `all`: Sync submodules, spin up Docker infrastructure, run database migrations, and execute system health checks (Default).
- `infra`: Spin up Docker infrastructure containers only.
- `submodules`: Initialize and synchronize git submodules.
- `migrate`: Execute Alembic database migrations (`alembic upgrade head`).
- `check`: Perform deep TCP/HTTP health checks across all data stores and microservice ports.
- `test`: Run system pytest test suite.

### Profile Options (`--profile`):
- `core`: PostgreSQL, Qdrant, Redis (Default, ~1.3GB RAM)
- `graph`: Neo4j graph database
- `messaging`: Kafka, Zookeeper, topic initializer
- `app`: API Gateway and GPU Inference containers
- `full`: Complete stack across all profiles

---

## 2. System Overview & Port Mapping

The platform consists of the following primary microservices and infrastructure stores:

| Service | Environment / Runtime | External Port | Internal Address | Description |
|---|---|---|---|---|
| **PostgreSQL** | Docker (`postgres:16`) | `5432` | `postgres:5432` | Relational database (Agents, Workflows, Test Suites, Documents, Chunks) |
| **Qdrant** | Docker (`qdrant/qdrant`) | `6333` | `qdrant:6333` | Vector database (SyntraFlow document chunks, embeddings) |
| **Neo4j** | Docker (`neo4j:5.20.0`) | `7474`, `7687` | `neo4j:7687` | Knowledge Graph for hybrid entity retrieval |
| **Redis** | Docker (`redis:7-alpine`) | `6379` | `redis:6379` | Cache, session store, rate limiter, and Pub/Sub telemetry broker |
| **Kafka** | Docker (`cp-kafka:7.6.0`) | `9092` | `kafka:9092` | Message broker (`syntraflow-ingestion-jobs`, `agent-eval-trigger`, `guardroute-traces`) |
| **Gateway** | FastAPI (CPU-only) | `8000` | `gateway:8000` | Gateway router proxying models, submodules, WebSocket/SSE telemetry, and Agent CRUD |
| **Inference Server**| FastAPI (GPU / CUDA) | `8010` | `inference:8010` | Heavyweight server running local models (SenseVoice, Baidu OCR, etc.) |
| **Frontend UI** | React 19 / Vite 8 / Tailwind v4 | `5173` | — | Decoupled developer dashboard with Zustand stores and ReactFlow canvas |

---

## 2. Configuration (`.env`)

Before running the application, make sure the `.env` file in the root directory contains your configurations. A pre-seeded template exists in [`.env.example`](file:///c:/Akshat/ContAIned/.env.example). 

### Key Env Variables:
* `ACTIVE_PROJECTS`: Comma-separated list of active modules: `["syntraflow", "guardroute", "evalops"]`.
* `DATABASE_URL`: `postgresql+asyncpg://contained:contained_pass@localhost:5432/contained_platform`
* `REDIS_URL`: `redis://localhost:6379/0`
* `KAFKA_BOOTSTRAP_SERVERS`: `localhost:9092`
* `GOOGLE_API_KEY` / `OPENROUTER_API_KEY`: API credentials for fallback models (e.g. Gemini).
* `CLASSIFIER_MODEL_PATH`: Location of model weights on disk (e.g. `models/Arch-Router-1.5B-Q8_0.gguf`).
* `OCR_PROVIDER`: Either `local` (to run local OCR model) or `api` (to fallback to Google API).

---

## 3. Option A: Local Development (Hybrid Native Setup)

Recommended workflow for fast iteration, debugging, and live hot-reloading.

### Step 1: Spin up Supporting Data Stores in Docker
Run only the infrastructure containers in the background:
```bash
docker compose -f infrastructure/docker-compose.yml up postgres qdrant neo4j redis zookeeper kafka kafka-setup -d
```

### Step 2: Set up Python Virtual Environment & Alembic Migrations
Install dependencies via Poetry:
```bash
poetry install --all-extras
```
Apply database migrations to set up PostgreSQL tables (`agents`, `workflows`, `syntraflow_documents`, `eval_test_suites`):
```bash
poetry run alembic upgrade head
```

### Step 3: Run the API Gateway Natively
Start the FastAPI API Gateway. It connects to PostgreSQL, Redis, Qdrant, Neo4j, and Kafka, and exposes WebSocket/SSE telemetry endpoints:
```bash
poetry run uvicorn gateway.main:app --host 0.0.0.0 --port 8000 --reload
```

### Step 4: Run the Inference Server Natively
Make sure model weights are placed under `models/` (or configured for on-demand fetch):
```bash
poetry run uvicorn inference.main:app --host 0.0.0.0 --port 8010 --reload
```

### Step 5: Start the React Frontend Dashboard
Navigate to `frontend/`, install dependencies, and start the Vite dev server with Tailwind CSS v4 support:
```bash
cd frontend
npm install
npm run dev -- --host 0.0.0.0 --port 5173
```
Access the dashboard at [http://localhost:5173](http://localhost:5173).

---

## 4. Option B: All-in-One Containerized Deployment

To deploy the full platform (gateway, inference server, databases, workers) inside Docker:

### System Requirements
* **Docker Engine** (or Docker Desktop)
* **NVIDIA Container Toolkit** (Required to map host GPU inside the GPU inference container)

### Step 1: Build & Launch Container Stack
Run:
```bash
docker compose -f infrastructure/docker-compose.yml up --build -d
```
Docker Compose will build custom images using:
* [`infrastructure/Dockerfile.gateway`](file:///c:/Akshat/ContAIned/infrastructure/Dockerfile.gateway) (Multi-stage, CPU-only)
* [`infrastructure/Dockerfile.inference`](file:///c:/Akshat/ContAIned/infrastructure/Dockerfile.inference) (Multi-stage, GPU/CUDA)

### Step 2: Verify Container Health
```bash
docker compose -f infrastructure/docker-compose.yml ps
```

---

## 5. Development Utilities & Observability

You can spin up optional monitoring and database admin UIs using Docker Compose profiles.

### Admin Dashboards Profile (`--profile admin`)
```bash
docker compose -f infrastructure/docker-compose.yml --profile admin up -d
```
* **pgAdmin:** [http://localhost:5050](http://localhost:5050) (Login: `admin@local.dev` / `admin`)
* **Kafka UI:** [http://localhost:8080](http://localhost:8080)

### Observability Profile (`--profile observability`)
```bash
docker compose -f infrastructure/docker-compose.yml --profile observability up -d
```
* **Jaeger Tracing UI:** [http://localhost:16686](http://localhost:16686)

---

## 6. Frontend Build & Verification

To build and validate the frontend production bundle:
```bash
cd frontend
npm run build
```
This executes `tsc -b && vite build` with `@tailwindcss/vite`, bundling assets into `frontend/dist/`.

To run the backend test suite:
```bash
poetry run pytest tests/ -v
```

---

## 7. Production Deployment Checklist

1. **Persistent Volume Mounts:** Model weights (`/app/models`) and database volumes (`pgdata`, `qdrant_data`, `neo4j_data`) must be backed by persistent storage (GCP Filestore, AWS EFS, or K8s PVs).
2. **Kubernetes Architecture:**
   - Deploy Gateway as a stateless `Deployment` behind an ingress router.
   - Deploy Ingestion Workers as horizontally scaled pods based on Kafka consumer lag for topic `syntraflow-ingestion-jobs`.
   - Deploy EvalOps background runners subscribed to `agent-eval-trigger`.
   - Deploy GPU Inference Server as a dedicated GPU node pool (`NVIDIA L4/T4`) with autoscaling.
