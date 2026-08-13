# Subtask 10_01: LiteLLM Routing Live Tests

## Parent Base Task
`tasks/v8/base/10_live_api_tests.md`

## Objective
Create `tests/live_api/test_litellm_routing.py` — routing & fallback chains.

## Tasks
1. [ ] Call primary (Gemini Flash) → verify response structure.
2. [ ] Simulate primary failure → verify fallback to OpenRouter.
3. [ ] Verify model selection respects `DEEPEVAL_MODEL` config.

## Definition of Done
- LiteLLM routing and fallback chains verified with real API keys.
- `DEEPEVAL_MODEL` config respected.
