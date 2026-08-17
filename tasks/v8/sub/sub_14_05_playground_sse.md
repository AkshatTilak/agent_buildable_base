# Subtask 14_05: Playground Chat SSE Tests

## Parent Base Task
`tasks/v8/base/14_websocket_sse_streaming_tests.md`

## Objective
Create `tests/streaming/test_playground_sse.py` — playground chat SSE.

## Tasks
1. [x] Send streaming playground request → receive SSE token chunks.
2. [x] Verify chunk format matches OpenAI-compatible SSE structure.
3. [x] Test with context attachments (file content injected into prompt).
4. [x] Test model parameter overrides (temperature, max_tokens) in streaming mode.
5. [x] Test error cases: invalid model ID, empty prompt.

## Definition of Done
- Playground chat SSE tests pass with OpenAI-compatible chunk format against actual running Gateway (`:8000`).
- Context attachments, parameter overrides, and error cases verified.
- If errors arise, inspect Docker container logs (`docker compose logs redis db`) and fix root causes directly.
- Deleting test data is not necessary.
