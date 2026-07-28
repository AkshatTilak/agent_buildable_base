# Setup & Deployment Guide: ContAIned AI Platform (V6)

This guide outlines how to build, run, test, and deploy the **ContAIned AI Platform V6**. It covers **automated CLI deployment**, **local hybrid native/Docker development**, **Tailwind CSS v4 frontend integration**, **all-in-one containerized deployment**, first-run bootstrap of the Hub tenancy model, and the V5 → V6 upgrade path.

> V6 introduces **Hubs** as the unit of tenancy. Every domain resource is scoped by `hub_id`. See [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) for the tenancy model itself; this guide only covers what an operator must configure and run.

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

### V6 Env Variables — Email Delivery (all optional)

SMTP is **entirely optional**. When it is unconfigured the platform falls back to `NullMailer`: invites, approvals and password resets still succeed, and the admin UI surfaces a **Copy invite link** control (with a warning banner) containing the one-time raw invite URL instead of sending an email. No deployment is blocked by the absence of a mail server.

| Variable | Default | Purpose |
|---|---|---|
| `SMTP_HOST` | *(empty)* | SMTP server hostname. Empty ⇒ `NullMailer`, copy-link fallback. |
| `SMTP_PORT` | `587` | SMTP port (`587` STARTTLS, `465` implicit TLS). |
| `SMTP_USER` | *(empty)* | SMTP username. Leave empty for unauthenticated relays. |
| `SMTP_PASSWORD` | *(empty)* | SMTP password. Secret — inject via secret manager, never commit. |
| `SMTP_FROM` | `ContAIned <no-reply@contained.local>` | RFC-5322 From header used for invites, approvals, and resets. |
| `SMTP_USE_TLS` | `true` | Use STARTTLS/SSL for the SMTP connection. |

### V6 Env Variables — Invitations, Approval Gate & Hubs

| Variable | Default | Purpose |
|---|---|---|
| `INVITE_TTL_HOURS` | `72` | Lifetime of an admin-issued invite token before it expires. |
| `PASSWORD_RESET_TTL_MINUTES` | `60` | Lifetime of a single-use password-reset token. |
| `APP_PUBLIC_URL` | `http://localhost:5173` | Public frontend origin. Used to build invite/reset links — **must** be the externally reachable URL in production, otherwise emailed links point at localhost. |
| `ALLOW_SELF_REGISTRATION` | `true` | Allow uninvited sign-up. Registrants land in `pending` until a platform admin approves them. Set `false` for invite-only deployments. |
| `ALLOW_MEMBER_HUB_CREATION` | `true` | Allow platform `member` users to create new hubs. `false` restricts hub creation to platform `admin`. |
| `AUTO_APPROVE_EMAIL_DOMAINS` | *(empty)* | Optional CSV of trusted domains (e.g. `acme.com`) whose self-registrations skip the approval gate and become `active` immediately. |
| `DATASTORE_ENCRYPTION_KEY` | *(empty)* | **Required in V6.** Fernet key used to encrypt `datastore_bindings.credentials_encrypted` at rest, plus invite/credential encryption. |

> **⚠️ `DATASTORE_ENCRYPTION_KEY` must be generated once and kept stable for the life of the deployment.**
> Generate it with:
> ```bash
> python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
> ```
> Store it in your secret manager and back it up alongside the database. If the key is lost or rotated without re-encrypting, **every stored datastore credential becomes permanently unrecoverable** and each affected `datastore_binding` must be re-entered by hand.

---

## 3. First-Run Bootstrap (V6)

A fresh install has no hubs and no users. The V6 migration and first sign-in establish both.

### Step 1: The first platform admin

The V6 Alembic migration guarantees exactly one platform admin exists:

* If the database already contains a user with the V5 `role = 'admin'`, the **first** such user is promoted to `platform_role = 'admin'`, `status = 'active'` and becomes the owner of the seeded hubs.
* If no admin exists (clean install), the migration creates a **synthetic admin** `system@contained.local` with no password hash and no linked identity. It exists only to satisfy the `hubs.owner_id` foreign key and cannot be signed into. Transfer hub ownership to a real account once one exists, and leave the synthetic account in place — it is referenced by the seed data.

The system refuses any action that would leave zero `active` platform admins.

### Step 2: The four default hubs

The migration seeds one hub of each type, all owned by the admin from Step 1:

| Hub | Type | Purpose |
|---|---|---|
| `ingestion/default` — "Default Ingestion Hub" | `ingestion` | Collections, documents, datastore bindings |
| `agent/default` — "Default Agent Hub" | `agent` | Agent definitions and endpoints |
| `workflow/default` — "Default Workflow Hub" | `workflow` | Workflows, versions, runs |
| `eval/default` — "Default Eval Hub" | `eval` | Eval suites, runs, traces |

`hub_links` are seeded between these four hubs along every allowed direction, so agent → collection, workflow → agent and eval → agent references continue to resolve after the cutover.

### Step 3: The first real user

1. Start the gateway and frontend, then visit `APP_PUBLIC_URL`.
2. Sign in as the promoted admin (OAuth or password), **or** — on a clean install with only the synthetic admin — self-register the first account and promote it directly in the database (`UPDATE users SET platform_role='admin', status='active' WHERE email=...`), then use the Admin Console for everything thereafter.
3. From `/admin/invites`, invite the rest of the team with a pre-assigned platform role and hub grants. If SMTP is unconfigured, copy the generated invite link and deliver it out-of-band — the raw token is shown **once**, only to the creating admin.

> **Approval gate:** any user who signs up **without** a matching invite lands in `status = pending` and receives no usable session until a platform admin approves them from `/admin/users/pending` (unless their email domain is listed in `AUTO_APPROVE_EMAIL_DOMAINS`). Set `ALLOW_SELF_REGISTRATION=false` to reject uninvited sign-ups outright.

See [`references/logic/user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) for the full account lifecycle.

---

## 4. Upgrading from V5 to V6

The V6 migration is a **two-stage hard cutover** — schema creation and backfill, then constraint tightening and legacy-column drops. No back-compat aliases are retained, and flat V5 routes are deleted rather than deprecated.

### Before you start

1. **Back up the database.** This is not optional. Take a full `pg_dump` (and a snapshot of the Qdrant storage volume) before running anything.
   ```bash
   pg_dump -Fc -U contained contained_platform > contained_v5_pre_upgrade.dump
   ```
2. **Set `DATASTORE_ENCRYPTION_KEY`** before migrating — the migration cannot write encrypted binding credentials without it.
3. **Rehearse on a restored dump.** Restore the dump into a scratch database and run the dry-run harness against it:
   ```bash
   poetry run python scripts/migration_dryrun.py --database-url postgresql+asyncpg://.../contained_scratch
   ```
   It reports row counts per backfilled table, orphaned rows that would violate the new `NOT NULL hub_id` constraints, and Qdrant collections that would be renamed. **Do not touch production until the dry run is clean.**

### Running the upgrade

```bash
docker compose -f infrastructure/docker-compose.yml up postgres qdrant redis -d
poetry run alembic upgrade head
```

What the migration does, in order:

1. Creates `hubs`, `hub_members`, `hub_links`, `datastore_bindings`, `audit_log`, `user_invites`, `user_identities`, `workflow_versions`, `workflow_runs`.
2. Adds `users.platform_role`, backfills from `role` (`admin → admin`, `editor → member`, `viewer → member`), replaces `is_active` with `status`, then drops `role` and `is_active`.
3. Seeds the four default hubs and the links between them (see §3).
4. Adds `hub_id` to every scoped table as nullable, backfills existing rows to the matching default hub, then `SET NOT NULL`.
5. Adds every existing active user to all four default hubs (`admin → owner`, `editor → contributor`, `viewer → viewer`).
6. **Renames physical Qdrant collections to `default__{name}`** and updates `syntraflow_collections.physical_name`.
7. Drops `syntraflow_collections.tenant_id` and rebuilds the per-hub unique constraints.

The migration is idempotent when re-run against an already-migrated database.

### Downgrade caveat

`downgrade()` **is** implemented and will restore the V5 schema shape, but it is a safety net for a failed cutover, not a supported rollback of a live V6 deployment:

* Only resources belonging to the four **default** hubs can be mapped back to the flat V5 namespace. **Any data created under V6 hubs beyond the defaults will not survive a downgrade** — non-default hubs and everything they own are dropped by the cascade.
* Hub memberships, links, datastore bindings, audit rows, workflow versions/runs, and invites have no V5 equivalent and are lost.
* Qdrant collections are renamed back from `default__{name}`; collections belonging to other hubs are left in place and orphaned.

If you need to roll back after users have created their own hubs, restore the pre-upgrade dump instead of running `downgrade()`.

### After the upgrade

* Verify the four default hubs and their links: `GET /hubs`.
* Verify per-hub datastore binding health: `GET /hubs/{hub_id}/datastores`.
* Verify Qdrant collection names are namespaced (`default__…`) in the embedded dashboard.
* Update any external integration still calling the removed flat routes (`/agents`, `/workflows`, `/api/syntraflow/collections`, `/api/evalops/*`) to their `/hubs/{hub_id}/...` equivalents.

---

## 5. Option A: Local Development (Hybrid Native Setup)

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

## 6. Option B: All-in-One Containerized Deployment

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

## 7. Development Utilities & Observability

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

## 8. Frontend Build & Verification

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

## 9. Production Deployment Checklist

0. **Secrets:** `DATASTORE_ENCRYPTION_KEY` generated once, stored in a secret manager, backed up with the database, and **never rotated without re-encrypting** existing `datastore_bindings`. `APP_PUBLIC_URL` set to the externally reachable frontend origin. `SMTP_PASSWORD` injected as a secret, not baked into the image.
1. **Persistent Volume Mounts:** Model weights (`/app/models`) and database volumes (`pgdata`, `qdrant_data`, `neo4j_data`) must be backed by persistent storage (GCP Filestore, AWS EFS, or K8s PVs).
2. **Kubernetes Architecture:**
   - Deploy Gateway as a stateless `Deployment` behind an ingress router.
   - Deploy Ingestion Workers as horizontally scaled pods based on Kafka consumer lag for topic `syntraflow-ingestion-jobs`.
   - Deploy EvalOps background runners subscribed to `agent-eval-trigger`.
   - Deploy GPU Inference Server as a dedicated GPU node pool (`NVIDIA L4/T4`) with autoscaling.
