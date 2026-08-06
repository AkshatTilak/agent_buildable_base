# System Topology & Architecture Reference

## Overview
TrueCare is structured as a Python-first monorepo using `uv` workspace mode. The repository contains 3 client web/mobile applications, 9 domain microservices, and 3 shared internal packages.

---

## Directory Mapping

```
truecare/
├── agent_buildable_base/          # Agent prompt templates, tasks (v1), evaluation benchmarks
├── apps/
│   ├── web-family/                # Next.js B2C discovery & comparison portal (SEO focus)
│   ├── web-facility/              # Next.js / React B2B SaaS facility management dashboard
│   ├── web-admin/                 # Next.js Internal ERP & data verification dashboard
│   └── mobile/                    # React Native / Flutter cross-platform mobile shell
├── services/
│   ├── auth-service/              # FastAPI — Auth, OIDC, JWT, RBAC scopes
│   ├── facility-service/          # FastAPI — Facility profiles, claiming logic
│   ├── search-service/            # FastAPI — Spatial PostGIS + Lexical BM25 + pgvector RRF hybrid engine
│   ├── ai-agent-service/          # Python — Auto-enricher agent, comparison agent, human review queue
│   ├── leads-service/             # FastAPI — CRM kanban, tour booking backend
│   ├── billing-service/           # FastAPI — Stark/AKS compliance firewall & SaaS billing
│   ├── ingestion-service/         # Python — CMS API sync, camelot/pdfplumber, OCR, Prefect ETL DAGs
│   ├── taxonomy-service/          # FastAPI — Care types, amenities, question bank
│   └── analytics-service/         # FastAPI — Audit logs, system telemetry
├── packages/
│   ├── py-common/                 # Shared ORM baselines, Pydantic models, JSON logger
│   ├── api-contracts/             # OpenAPI specs & JSON schemas
│   └── ts-common/                 # Generated TypeScript definitions for web/mobile apps
├── infra/                         # Docker Compose, Terraform, CI workflows
├── data/                          # Alembic migrations, database seeds
└── docs/                          # Architecture runbooks and diagrams
```

---

## Network & Service Intercommunication
- Client apps communicate with microservices via REST/JSON APIs typed by `packages/ts-common`.
- Inter-service communication uses gRPC / HTTP internal REST.
- Async event tasks are dispatched through Redis + Celery / Prefect workflows.
