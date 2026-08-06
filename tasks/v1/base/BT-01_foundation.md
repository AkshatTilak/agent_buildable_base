# Base Task: BT-01 — Foundation, Workspace & Core Services Base

## Objective
Establish the project workspace (`uv` monorepo configuration), PostgreSQL + PostGIS core database schema, Alembic migration framework, shared Python/TypeScript packages, and foundational domain microservices (`auth-service` and `taxonomy-service`).

## Business / System Value
Provides a unified data model, consistent typing across backend and frontend, enterprise-grade role-based security (RBAC), and standard taxonomy definitions across all client surfaces and microservices.

---

## Subtask Registry

- [x] [ST-01: Monorepo Scaffolding & Core Database Setup](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-01_monorepo_and_db_scaffold.md)
  - Details: `uv` workspace, docker-compose (Postgres + PostGIS, Redis, OpenSearch), Alembic migrations, root linting (`ruff`, `mypy`).
- [x] [ST-02: Auth & RBAC Service Implementation](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-02_auth_and_rbac_service.md)
  - Details: OIDC, JWT issuing, role permissions model (`family_guest` to `platform_super_admin`), token verification middleware.
- [x] [ST-03: Taxonomy Service & Shared Core Packages](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-03_taxonomy_and_common_packages.md)
  - Details: `packages/py-common`, `packages/api-contracts`, `packages/ts-common`, `services/taxonomy-service` (care types, amenities, question bank).

---

## Complexity Rating
**High** — Lays the foundational architecture, database schemas, and shared contracts for all subsequent services.

## Acceptance Criteria
- Monorepo dependencies sync cleanly via `uv sync`.
- Local Docker environment starts PostgreSQL + PostGIS, Redis, and OpenSearch.
- Alembic migrations apply cleanly and establish core tables (`facilities`, `users`, `audit_logs`, `taxonomy`).
- `auth-service` issues and validates JWTs with role scopes.
- `taxonomy-service` serves structured care types, amenities, and versioned questionnaire items.
