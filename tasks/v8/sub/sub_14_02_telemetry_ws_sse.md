# Subtask 14_02: Telemetry WebSocket & SSE Tests

## Parent Base Task
`tasks/v8/base/14_websocket_sse_streaming_tests.md`

## Objective
Create `tests/streaming/test_telemetry_ws.py` + `test_telemetry_sse.py` — telemetry streaming.

## Tasks
1. [x] **WebSocket telemetry** (`test_telemetry_ws.py`):
   - Connect via WebSocket → receive initial metrics payload → validate JSON schema (cpu, memory, vram, agents, jobs).
   - Verify periodic metric updates arrive (~3s interval via Redis pub/sub or fallback polling).
   - Test auth token passing via query parameter.
   - Test reconnection behavior (close connection → verify server handles disconnect cleanly).
   - Test concurrent WebSocket connections (multiple clients simultaneously).
   - Verify Redis pub/sub channel (`telemetry-system-health`) receives published data.
2. [x] **SSE telemetry fallback** (`test_telemetry_sse.py`):
   - Connect via EventSource → receive `telemetry` events → validate JSON schema.
   - Verify SSE format compliance (`event: telemetry\ndata: {...}\n\n`).
   - Test client disconnect → server generator cancellation (no resource leak).
   - Verify SSE works when Redis is unavailable (fallback to direct metrics polling).

## Definition of Done
- Telemetry WebSocket and SSE tests pass against actual running gateway (`:8000`) with actual Redis pub/sub (`:6379`).
- JSON schema, format compliance, reconnection, concurrency, and connection lifecycle verified.
- If errors arise, inspect Docker container logs (`docker compose logs redis`) and fix root causes directly in gateway telemetry code.
- Deleting test data is not necessary.
