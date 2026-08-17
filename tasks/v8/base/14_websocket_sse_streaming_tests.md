# Base Task 14: WebSocket & SSE Streaming Tests (v8)

## Objective
Test all real-time streaming interactions — WebSocket telemetry, SSE fallback, workflow run SSE, agent invoke SSE, playground SSE, and external API SSE — end-to-end against a running gateway with real Redis pub/sub. These are critical failure points today.

## Why
Streaming is a critical failure point. Existing tests never exercise WebSocket/SSE against a running gateway with real Redis pub/sub, so connection handling, event ordering, reconnection, and resource cleanup bugs go undetected.

## Identified streaming surfaces (from codebase audit)
1. **Telemetry WebSocket** — `/api/telemetry/ws` — Real-time system metrics via Redis pub/sub.
2. **Telemetry SSE** — `/api/telemetry/stream` — SSE fallback for telemetry.
3. **Workflow Run SSE** — `/{wf_id}/runs/{run_id}/stream` — Live execution events (`run_start`, `node_start`, `node_end`, `run_end`).
4. **Agent Invoke SSE** — `/agents/{agent_id}/invoke` with `stream: true` — Token-by-token LLM response.
5. **Playground SSE** — `/api/playground/chat` with `stream: true` — Interactive chat streaming.
6. **External API SSE** — `/v1/chat/completions` with `stream: true` — OpenAI-compatible streaming.

## Scope
- **`tests/streaming/conftest.py`** — Shared streaming fixtures:
  - `ws_connect` fixture — async context manager for WebSocket connections via `websockets` library.
  - `sse_connect` fixture — async HTTP client that reads `text/event-stream` responses and yields parsed events.
  - `wait_for_event` helper — waits for a specific SSE event type with timeout.
  - `collect_all_events` helper — collects all SSE events until stream closes or timeout.
- **`tests/streaming/test_telemetry_ws.py`** — WebSocket telemetry:
  - Connect via WebSocket → receive initial metrics payload → validate JSON schema (cpu, memory, vram, agents, jobs).
  - Verify periodic metric updates arrive (~3s interval via Redis pub/sub or fallback polling).
  - Test auth token passing via query parameter.
  - Test reconnection behavior (close connection → verify server handles disconnect cleanly).
  - Test concurrent WebSocket connections (multiple clients simultaneously).
  - Verify Redis pub/sub channel (`telemetry-system-health`) receives published data.
- **`tests/streaming/test_telemetry_sse.py`** — SSE telemetry fallback:
  - Connect via EventSource → receive `telemetry` events → validate JSON schema.
  - Verify SSE format compliance (`event: telemetry\ndata: {...}\n\n`).
  - Test client disconnect → server generator cancellation (no resource leak).
  - Verify SSE works when Redis is unavailable (fallback to direct metrics polling).
- **`tests/streaming/test_workflow_sse.py`** — Workflow run SSE:
  - Start a workflow run → open SSE stream → verify event sequence: `run_start` → `node_start` → `node_end` → ... → `run_end`.
  - Validate each event's JSON structure (node_id, status, timestamps, output).
  - Test stream for multi-node workflows (verify correct ordering).
  - Test stream for failed workflow runs (error events).
  - Test reconnecting to a completed run's stream (should get final state or empty).
  - Test concurrent SSE connections to same run.
- **`tests/streaming/test_agent_invoke_sse.py`** — Agent invoke SSE:
  - Invoke agent with `stream: true` → receive SSE chunks → validate `delta` / `status` fields.
  - Verify chunk accumulation produces coherent full response.
  - Verify `[DONE]` sentinel at stream end.
  - Verify `status: completed` with `latency_ms` in final chunk.
  - Test streaming error handling (invalid model, missing API key → error chunk in SSE).
  - Verify invocation is logged to DB after stream completes.
- **`tests/streaming/test_playground_sse.py`** — Playground chat SSE:
  - Send streaming playground request → receive SSE token chunks.
  - Verify chunk format matches OpenAI-compatible SSE structure.
  - Test with context attachments (file content injected into prompt).
  - Test model parameter overrides (temperature, max_tokens) in streaming mode.
  - Test error cases: invalid model ID, empty prompt.
- **`tests/streaming/test_external_api_sse.py`** — External API SSE:
  - Call external endpoint with API key auth + `stream: true` → validate SSE chunks.
  - Verify OpenAI-compatible response format in stream mode.
  - Verify usage stats in final chunk.
  - Test fallback chain behavior during streaming (primary fails → secondary picks up).

## Associated Subtasks
1. `[x]` `sub_14_01_streaming_conftest.md`: `tests/streaming/conftest.py` — shared streaming fixtures & helpers.
2. `[x]` `sub_14_02_telemetry_ws_sse.md`: `test_telemetry_ws.py` + `test_telemetry_sse.py` — telemetry streaming.
3. `[x]` `sub_14_03_workflow_sse.md`: `test_workflow_sse.py` — workflow run SSE.
4. `[x]` `sub_14_04_agent_invoke_sse.md`: `test_agent_invoke_sse.py` — agent invoke SSE.
5. `[x]` `sub_14_05_playground_sse.md`: `test_playground_sse.py` — playground chat SSE.
6. `[x]` `sub_14_06_external_api_sse.md`: `test_external_api_sse.py` — external API SSE.

## Definition of Done
- All six streaming surfaces tested end-to-end against the actual running gateway (`:8000`) with actual Redis pub/sub (`:6379`).
- Event ordering, JSON schema, reconnection, concurrency, and connection lifecycle verified.
- SSE format compliance and `[DONE]` sentinel verified.
- Streaming error handling and DB logging verified.
- When streaming failures occur, inspect **Docker container logs** (`docker compose logs redis`) and fix root causes directly in gateway routing and event generator logic. Deleting test data is not necessary.
