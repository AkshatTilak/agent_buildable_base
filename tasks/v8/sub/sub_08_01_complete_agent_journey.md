# Subtask 08_01: Complete Agent Journey E2E Test

## Parent Base Task
`tasks/v8/base/08_end_to_end_workflow_tests.md`

## Objective
Create `tests/e2e/flows/test_complete_agent_journey.py` — full agent journey from signup to eval.

## Tasks
1. [ ] Register new user via auth API (unique email / namespace).
2. [ ] Create an "agent" hub.
3. [ ] Create an agent with `model_id: gemini/gemma-3-27b-it`, system prompt, temperature=0.3.
4. [ ] Link an ingestion hub → upload documents (embed with `harrier-0.6b`) → verify retrieval.
5. [ ] Test the agent via playground (real LLM call to `gemini/gemma-3-27b-it`).
6. [ ] Create an eval hub → link to agent hub → run eval with `DEEPEVAL_MODEL=gemini/gemini-3.5-flash` → verify scores.
7. [ ] Verify end-to-end execution without mandatory post-test data deletion.

## Definition of Done
- Full agent journey passes end-to-end against the actual running gateway (`:8000`), inference (`:8001`), and actual Docker services.
- If errors arise, inspect Docker container logs (`docker compose logs`) and fix the root causes in the backend, frontend, or submodules.
- Deleting test data is not necessary.
