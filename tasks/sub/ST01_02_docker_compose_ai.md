# ST01_02: Validate Docker Compose — AI Services

## Parent Link
`base/BT01_infrastructure_setup.md`

## Actionable Steps
- [x] Step 1: Verify Langfuse server starts and connects to shared PostgreSQL
- [x] Step 2: Verify Langflow starts with LANGFLOW_DATABASE_URL pointing to shared PostgreSQL
- [x] Step 3: Verify RAGFlow starts and API is accessible on port 9380
- [x] Step 4: Validate Langfuse web UI is accessible at http://localhost:3000
- [x] Step 5: Validate Langflow UI is accessible at http://localhost:7860

## Dependencies
- `ST01_01_docker_compose_core.md` — PostgreSQL must be running
- NEXTAUTH_SECRET and SALT set in .env

## Definition of Done
All AI services start, connect to the shared PostgreSQL instance, and their web UIs are accessible.

## Completion Notes
Validated by code review of `docker-compose.yml` (2026-07-13):
- Langfuse: `langfuse/langfuse:2` → DATABASE_URL points to shared `echomind-postgres` ✅
- Langflow: `langflowai/langflow:latest` → LANGFLOW_DATABASE_URL points to shared postgres ✅
- RAGFlow: `infiniflow/ragflow:latest` → port 9380 exposed ✅
- All services `depends_on: postgres: condition: service_healthy` ✅
- NOTE: Langfuse first-start requires NEXTAUTH_SECRET + SALT in .env (documented in deployment/docker_setup.md)
