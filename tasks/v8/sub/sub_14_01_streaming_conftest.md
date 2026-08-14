# Subtask 14_01: Streaming Shared Fixtures

## Parent Base Task
`tasks/v8/base/14_websocket_sse_streaming_tests.md`

## Objective
Create `tests/streaming/conftest.py` — shared streaming fixtures & helpers.

## Tasks
1. [ ] `ws_connect` fixture — async context manager for WebSocket connections via `websockets` library.
2. [ ] `sse_connect` fixture — async HTTP client that reads `text/event-stream` responses and yields parsed events.
3. [ ] `wait_for_event` helper — waits for a specific SSE event type with timeout.
4. [ ] `collect_all_events` helper — collects all SSE events until stream closes or timeout.

## Definition of Done
- Shared streaming fixtures and helpers available to all streaming tests against actual running Gateway (`:8000`).
- If errors arise, inspect Docker container logs (`docker compose logs redis`) and fix root causes directly.
- Deleting test data is not necessary.
