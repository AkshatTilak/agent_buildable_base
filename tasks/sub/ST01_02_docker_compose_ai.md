# ST01_02: Validate Docker Compose — AI Services

## Parent Link
`base/BT01_infrastructure_setup.md`

## Actionable Steps
- [ ] Step 1: Verify Langfuse server starts and connects to shared PostgreSQL
- [ ] Step 2: Verify Langflow starts with LANGFLOW_DATABASE_URL pointing to shared PostgreSQL
- [ ] Step 3: Verify RAGFlow starts and API is accessible on port 9380
- [ ] Step 4: Validate Langfuse web UI is accessible at http://localhost:3000
- [ ] Step 5: Validate Langflow UI is accessible at http://localhost:7860

## Dependencies
- `ST01_01_docker_compose_core.md` — PostgreSQL must be running
- NEXTAUTH_SECRET and SALT set in .env

## Definition of Done
All AI services start, connect to the shared PostgreSQL instance, and their web UIs are accessible.
