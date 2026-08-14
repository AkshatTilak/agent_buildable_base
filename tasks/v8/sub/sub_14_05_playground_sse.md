# Subtask 14_05: Playground Chat SSE Tests

## Parent Base Task
`tasks/v8/base/14_websocket_sse_streaming_tests.md`

## Objective
Create `tests/streaming/test_playground_sse.py` — playground chat SSE.

## Tasks
1. [ ] Send streaming playground request → receive SSE token chunks.
2. [ ] Verify chunk format matches OpenAI-compatible SSE structure.
3. [ ] Test with context attachments (file content injected into prompt).
4. [ ] Test model parameter overrides (temperature, max_tokens) in streaming mode.
5. [ ] Test error cases: invalid model ID, empty prompt.

## Definition of Done
- Playground chat SSE tests pass with OpenAI-compatible chunk format against actual running Gateway (`:8000`).
- Context attachments, parameter overrides, and error cases verified.
- If errors arise, inspect Docker container logs (`docker compose logs redis db`) and fix root causes directly.
- Deleting test data is not necessary.
