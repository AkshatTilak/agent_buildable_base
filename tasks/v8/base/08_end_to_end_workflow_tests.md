# Base Task 8: End-to-End Workflow Tests (Full User Journeys) (v8)

## Objective
Multi-step tests that exercise the entire system from signup to eval results, touching every actual service layer (Postgres `:5432`, Qdrant `:6333`, Redis `:6379`, Neo4j `:7687`, Gateway `:8000`, Inference `:8001`, LiteLLM).

## Why
Unit and integration tests validate individual layers in isolation. E2E tests are the only way to catch cross-service integration bugs — auth → hub → agent → ingestion → retrieval → eval — that only surface when the full stack runs together. When E2E tests uncover flaws, agents must inspect Docker container logs and fix the root causes in the backend, frontend, inference, or submodules.

## Scope
- **`tests/e2e/flows/test_complete_agent_journey.py`**:
  1. Register new user via auth API (unique email/slug).
  2. Create an "agent" hub.
  3. Create an agent with `model_id: gemini/gemma-3-27b-it`, system prompt, temperature=0.3.
  4. Link an ingestion hub → upload documents (embed with `harrier-0.6b`) → verify retrieval.
  5. Test the agent via playground (real LLM call to `gemini/gemma-3-27b-it`).
  6. Create an eval hub → link to agent hub → run eval with `DEEPEVAL_MODEL=gemini/gemini-3.5-flash` → verify scores.
  7. *(Deleting test data is not necessary).*
- **`tests/e2e/flows/test_complete_workflow_journey.py`**:
  1. Register user → create workflow hub.
  2. Design a multi-node workflow (LLM node using `gemini/gemini-3.5-flash` → conditional → two branches).
  3. Execute workflow run → verify step-by-step execution log.
  4. Save workflow version → modify → save new version → restore old version.
  5. Export workflow → import into new hub → verify portability.
- **`tests/e2e/flows/test_multi_user_collaboration.py`**:
  1. Admin creates hub → invites member.
  2. Member accepts invite → gains access.
  3. Both users create resources in the same hub → verify visibility.
  4. Admin demotes member to viewer → verify write access revoked.
  5. Admin removes member → verify access fully revoked.
- **`tests/e2e/flows/test_hub_linking_cross_access.py`**:
  1. Create ingestion hub → ingest documents (embed with `gemini/gemini-embedding-2` API path).
  2. Create agent hub → create agent with `gemini/gemma-3-27b-it`.
  3. Link agent hub ↔ ingestion hub.
  4. Agent retrieves documents from linked ingestion hub (cross-hub retrieval).
  5. Revoke link → verify agent can no longer access ingestion data.

## Associated Subtasks
1. `[ ]` `sub_08_01_complete_agent_journey.md`: `test_complete_agent_journey.py` — full agent journey.
2. `[ ]` `sub_08_02_complete_workflow_journey.md`: `test_complete_workflow_journey.py` — full workflow journey.
3. `[ ]` `sub_08_03_multi_user_collaboration.md`: `test_multi_user_collaboration.py` — multi-user collaboration.
4. `[ ]` `sub_08_04_hub_linking_cross_access.md`: `test_hub_linking_cross_access.py` — cross-hub access.

## Definition of Done
- All four E2E journeys pass end-to-end against the actual running gateway (`:8000`), inference (`:8001`), and actual Docker services.
- Test entities use unique namespaces/UUIDs; deleting test data after runs is not necessary.
- Cross-service integration bugs across auth, hubs, agents, ingestion, retrieval, and eval are diagnosed with Docker container logs (`docker compose logs`) and fixed directly in the underlying backend/frontend/submodule code.
