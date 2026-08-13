# Subtask 01_01: Test Environment & Docker Compose Test Stack

## Parent Base Task
`tasks/v8/base/01_test_infrastructure_foundation.md`

## Objective
Create the dedicated test environment file and the full Docker Compose test stack that stands up all infra services with ephemeral volumes.

## Tasks
1. [x] Create `.env.test` (gitignored) with real service credentials:
   - Postgres (`TEST_DATABASE_URL`), Qdrant, Redis, Neo4j connection strings.
   - LiteLLM API keys (`GOOGLE_API_KEY`, `OPENROUTER_API_KEY`).
   - `DEEPEVAL_MODEL=gemini/gemini-3.5-flash`.
   - `AUTO_APPROVE_EMAIL_DOMAINS=[]`.
   - `OCR_PROVIDER` (local/off).
2. [x] Create `.env.test.example` (committed) with placeholder values and documentation of each variable.
3. [x] Create `infrastructure/docker-compose.test.yml`:
   - Postgres, Qdrant, Redis, Neo4j, Kafka + Zookeeper.
   - Ephemeral volumes (no host bind mounts — data destroyed on `down -v`).
   - Test-specific ports to avoid clashing with dev services.
   - Health checks for each service.
4. [x] Add `.env.test` to `.gitignore`.

## Definition of Done
- `docker compose -f infrastructure/docker-compose.test.yml up -d` starts all services with ephemeral volumes.
- `docker compose -f infrastructure/docker-compose.test.yml down -v` destroys all data.
- `.env.test.example` documents every variable in `.env.test`.
