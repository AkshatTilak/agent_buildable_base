# Subtask 10_01: LiteLLM Routing Live Tests

## Parent Base Task
`tasks/v8/base/10_live_api_tests.md`

## Objective
Create `tests/live_api/test_litellm_routing.py` — routing & fallback chains.

## Tasks
1. [x] Call primary (Gemini Flash) → verify response structure.
2. [x] Simulate primary failure → verify fallback to OpenRouter.
3. [x] Verify model selection respects `DEEPEVAL_MODEL` config.

## Definition of Done
- LiteLLM routing and fallback chains verified with real API keys against actual running Gateway (`:8000`).
- `DEEPEVAL_MODEL` config respected.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in LiteLLM router.
- Deleting test data is not necessary.
