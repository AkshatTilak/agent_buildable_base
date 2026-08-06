# Subtask: ST-01 — Monorepo Scaffolding & Core Database Setup

**Parent Link:** [BT-01: Foundation, Workspace & Core Services Base](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-01_foundation.md)

## Objective
Set up the Python `uv` workspace monorepo structure, Docker Compose local infrastructure (Postgres 16 + PostGIS 3.4, Redis, OpenSearch), Alembic database migration configuration, and root-level code quality tooling (`ruff`, `mypy`).

---

## Technical Actionable Steps

- [x] **Step 1: Initialize Root `pyproject.toml` & `uv` Workspace**
  - Configure root `pyproject.toml` with `[tool.uv.workspace]` matching `members = ["services/*", "packages/*"]`.
  - Set up `ruff` and `mypy` strict linting rules.

- [x] **Step 2: Configure Infrastructure Docker Compose (`infra/docker-compose.yml`)**
  - Define PostgreSQL with PostGIS image (`postgis/postgis:16-3.4`).
  - Define Redis cache container (`redis:7-alpine`).
  - Define OpenSearch container (`opensearchproject/opensearch:2.11.0`).

- [x] **Step 3: Build PostgreSQL Core Schemas & Alembic Migration Scaffold**
  - Set up `data/` directory with Alembic setup (`env.py`, `alembic.ini`).
  - Create initial migration for core tables: `providers`, `provider_ratings`, `provider_quality_measures`, `provider_surveys`, `provider_deficiencies`, `provider_penalties`, `provider_ownership`, `provider_staffing`, `staged_records`, `ingestion_runs`, `users`, `audit_logs`, `leads`.
  - Add PostGIS geography column (`location GEOGRAPHY(POINT, 4326)`) and spatial indexes (`idx_providers_location`).

- [x] **Step 4: Verify Environment Bootstrap**
  - Run `uv sync` to ensure dependency resolution.
  - Run `docker compose up -d` and execute `alembic upgrade head`.

---

## Dependencies
- Installed runtime tools: Python 3.12+, Docker, `uv`.

## Complexity Rating
**Medium**

## Definition of Done
- `uv sync` succeeds without errors.
- `docker compose` spins up PostgreSQL/PostGIS, Redis, and OpenSearch cleanly.
- `alembic upgrade head` runs without failure and creates all required relational and spatial tables.
