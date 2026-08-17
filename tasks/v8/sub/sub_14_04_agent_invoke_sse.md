# Subtask 14_04: Agent Invoke SSE Tests

## Parent Base Task
`tasks/v8/base/14_websocket_sse_streaming_tests.md`

## Objective
Create `tests/streaming/test_agent_invoke_sse.py` — agent invoke SSE.

## Tasks
1. [x] Invoke agent with `stream: true` → receive SSE chunks → validate `delta` / `status` fields.
2. [x] Verify chunk accumulation produces coherent full response.
3. [x] Verify `[DONE]` sentinel at stream end.
4. [x] Verify `status: completed` with `latency_ms` in final chunk.
5. [x] Test streaming error handling (invalid model, missing API key → error chunk in SSE).
6. [x] Verify invocation is logged to DB after stream completes.

## Definition of Done
- Agent invoke SSE tests pass with coherent chunk accumulation, `[DONE]` sentinel, and `status: completed` with `latency_ms` against actual running Gateway (`:8000`) and Inference server (`:8010`).
- Streaming error handling and DB logging verified.
- If errors arise, inspect Docker container logs (`docker compose logs redis db`) and fix root causes directly.
- Deleting test data is not necessary.
