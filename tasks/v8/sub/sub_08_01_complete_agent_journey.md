# Subtask 08_01: Complete Agent Journey E2E Test

## Parent Base Task
`tasks/v8/base/08_end_to_end_workflow_tests.md`

## Objective
Create `tests/e2e/flows/test_complete_agent_journey.py` — full agent journey from signup to eval.

## Tasks
1. [ ] Register new user via auth API.
2. [ ] Create an "agent" hub.
3. [ ] Create an agent with `model_id: gemini/gemma-3-27b-it`, system prompt, temperature=0.3.
4. [ ] Link an ingestion hub → upload documents (embed with `harrier-0.6b`) → verify retrieval.
5. [ ] Test the agent via playground (real LLM call to `gemini/gemma-3-27b-it`).
6. [ ] Create an eval hub → link to agent hub → run eval with `DEEPEVAL_MODEL=gemini/gemini-3.5-flash` → verify scores.
7. [ ] Cleanup: delete everything in reverse order.

## Definition of Done
- Full agent journey passes end-to-end against the running gateway and all real services.
- All resources cleaned up in reverse order.
