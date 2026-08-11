# Fresh Deployment Guide: ContAIned AI Platform (V6)

This guide provides the authoritative instructions for setting up, building, running, and verifying a **fresh installation** of the **ContAIned AI Platform (V6)**.

---

## 1. System Topology & Architecture Overview

The ContAIned platform operates as a multi-tenant microservices platform with a modular hub architecture.

### Microservices & Ports

| Component | Stack | Port | Internal / Container Host | Purpose |
|---|---|---|---|---|
| **Gateway API** | FastAPI / Python 3.11 | `8000` | `gateway:8000` | Platform routing, Auth/RBAC, Hub management, WebSocket/SSE telemetry |
| **Inference Server** | FastAPI / PyTorch / Transformers | `8010` | `inference:8010` | High-performance local model inference server |
| **Frontend UI** | React 19 / Vite / Tailwind CSS v4 | `5173` | — | Interactive multi-tenant hub workspace & workflow builder |
| **PostgreSQL** | PostgreSQL 16 | `5432` | `postgres:5432` | Primary relational store (Users, Hubs, Agents, Workflows, Audit Logs) |
| **Qdrant** | Qdrant Vector DB | `6333` | `qdrant:6333` | Vector database for SyntraFlow document collections and embeddings |
| **Redis** | Redis 7 Alpine | `6379` | `redis:6379` | Telemetry pub/sub, rate limiting, and session caching |
| **Neo4j** | Neo4j 5.20 | `7474`, `7687` | `neo4j:7687` | Knowledge graph entity store (Core Infrastructure) |
| **Kafka** | Confluent Kafka 7.6 | `9092` | `kafka:9092` | Asynchronous job message broker (Core Infrastructure) |

---

## 2. Prerequisites & Environment Configuration

### Prerequisites
- **Docker Engine** (v24.0+) & **Docker Compose** (v2.20+)
- **Python 3.11** (3.11.9 recommended)
- **Poetry** (v2.0+) with `poetry-plugin-export`
- **Node.js** (v20+) & `npm` (for frontend UI)

### Step 1: Environment File Setup
Create `.env` in the repository root by copying the template:
```bash
cp .env.example .env
```

### Step 2: Essential Environment Variables
Ensure the following key variables are set in `.env`:

```env
# Active Microservice Modules
ACTIVE_PROJECTS=["syntraflow","guardroute","evalops"]

# Database & Cache Connections
DATABASE_URL=postgresql+asyncpg://contained:contained_pass@localhost:5432/contained_platform
REDIS_URL=redis://localhost:6379/0

# Security & Encryption (REQUIRED IN V6)
DATASTORE_ENCRYPTION_KEY=YOUR_GENERATED_FERNET_KEY

# Frontend Public URL
APP_PUBLIC_URL=http://localhost:5173

# Registration & Approvals
ALLOW_SELF_REGISTRATION=true
ALLOW_MEMBER_HUB_CREATION=true
```

> **⚠️ Generate `DATASTORE_ENCRYPTION_KEY`:**
> Run the following command to generate a valid Fernet key and paste it into `.env`:
> ```bash
> python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
> ```

---

## 3. Fresh Deployment (Automated & Containerized)

### Method A: Automated Single-Command Deployment (Recommended)

Run the unified cross-platform deployment script to initialize submodules, spin up Docker infrastructure, execute database migrations, and perform health diagnostics:

```bash
poetry run python scripts/deploy.py --mode all --profile core
```

### Method B: Manual Containerized Deployment (Docker Compose)

#### 1. Deploy Infrastructure Containers
Spin up the core database, graph, message broker, and cache layer (PostgreSQL, Qdrant, Redis, Neo4j, Zookeeper, Kafka):
```bash
docker compose -f infrastructure/docker-compose.yml --profile core up -d
```

> **💡 Kafka & Zookeeper Volume Consistency:**
> Both `zookeeper` and `kafka` services use persistent Docker volumes (`zookeeper_data` and `kafka_data`) in `docker-compose.yml` to ensure Zookeeper cluster ID state remains synced. If you ever encounter an `InconsistentClusterIdException` after partial volume removals, run `docker compose -f infrastructure/docker-compose.yml --profile core down -v` to reset volumes cleanly.

#### 2. Run Database Migrations
Apply Alembic migrations to set up the V6 database schema and seed default hubs:
```bash
poetry run alembic upgrade head
```

#### 3. Build & Deploy Application Containers
Build and launch the API Gateway and Inference Server:
```bash
docker compose -f infrastructure/docker-compose.yml --profile core --profile app up -d --build
```

---

## 4. First-Run Bootstrap & Tenant Hub Initialization

Upon completing a fresh migration, the system automatically initializes:

1. **Synthetic Platform Admin**:
   - A synthetic admin user (`system@contained.local`) is created to own default platform resources.

2. **Four Seeded Default Hubs**:
   - `ingestion/default` — Ingestion Hub for collections & datastores.
   - `agent/default` — Agent Hub for agent lifecycles & executions.
   - `workflow/default` — Workflow Hub for workflow composition & versioning.
   - `eval/default` — Eval Hub for evaluation suites & flow tracing.
   - Cross-hub links are pre-seeded between these hubs for out-of-the-box operation.

3. **Provisioning Your Real Admin User**:
   - Self-register your first account on the frontend at `http://localhost:5173/register`.
   - Promote your user to Platform Admin via SQL:
     ```sql
     UPDATE users SET platform_role = 'admin', status = 'active' WHERE email = 'your-email@domain.com';
     ```
   - Refresh the page to access the Admin Console at `/admin/users` and `/admin/invites`.

---

## 5. Local Hybrid Development Setup

For active local development with hot-reloading:

### 1. Start Infrastructure Dependencies
```bash
docker compose -f infrastructure/docker-compose.yml --profile core up -d
```

### 2. Run API Gateway (Native FastAPI)
```bash
poetry run uvicorn gateway.main:app --host 0.0.0.0 --port 8000 --reload
```

### 3. Run Inference Server (Native FastAPI)
```bash
poetry run uvicorn inference.main:app --host 0.0.0.0 --port 8010 --reload
```

### 4. Start React Frontend Dashboard
```bash
cd frontend
npm install
npm run dev -- --host 0.0.0.0 --port 5173
```
Access the application at **`http://localhost:5173`**.

---

## 6. System Verification & Health Diagnostics

Execute system diagnostics to verify all ports and services:

```bash
poetry run python scripts/deploy.py --mode check
```

### Expected Output
```text
===== SYSTEM HEALTH DIAGNOSTIC REPORT =====
  * PostgreSQL DB (Port 5432)           : [UP/OK]
  * Qdrant Vector DB (Port 6333)        : [UP/OK]
  * Redis Cache (Port 6379)             : [UP/OK]
  * Gateway API (Port 8000)             : [UP/OK]
  * Inference Server (Port 8010)        : [UP/OK]
===========================================
```
