# Subtask 01_01: Test Environment & Infrastructure Configuration

## Parent Base Task
`tasks/v8/base/01_test_infrastructure_foundation.md`

## Objective
Create the dedicated test environment file targeting actual running development services on standard ports.

## Tasks
1. [x] Create `.env.test` (gitignored) with real service credentials:
   - Postgres (`TEST_DATABASE_URL` / standard port `:5432`), Qdrant (`:6333`), Redis (`:6379`), Neo4j (`:7687`), Kafka (`:9092`).
   - Gateway (`http://localhost:8000`) and Inference (`http://localhost:8001`).
   - LiteLLM API keys (`GOOGLE_API_KEY`, `OPENROUTER_API_KEY`).
   - `DEEPEVAL_MODEL=gemini/gemini-3.5-flash`.
   - `AUTO_APPROVE_EMAIL_DOMAINS=[]`.
   - `OCR_PROVIDER` (local/off).
2. [x] Create `.env.test.example` (committed) with placeholder values and documentation of each variable.
3. [x] Ensure test suite leverages the actual Docker compose infrastructure on standard ports.
4. [x] Add `.env.test` to `.gitignore`.

## Definition of Done
- `.env.test` maps correctly to actual running development containers and services on standard ports.
- Deleting test data is not necessary.
- `.env.test.example` documents every variable in `.env.test`.
