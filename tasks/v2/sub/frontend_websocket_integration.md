# Subtask: Frontend WebSocket & SSE Integration

**Parent Link:** `base/01_frontend_granular_realtime.md`

## Actionable Steps
- [ ] Implement a WebSocket endpoint `/api/gateway/ws/telemetry` or Server-Sent Events (SSE) `/api/gateway/telemetry/stream` in the Gateway (`gateway/main.py`).
- [ ] Connect the Gateway endpoint to Redis Pub/Sub channels (e.g., `telemetry-system-health`, `telemetry-ingestion-jobs`).
- [ ] Set up background threads/tasks in Gateway/Inference to collect CPU/GPU/VRAM status periodically and publish to Redis.
- [ ] Implement the React-side listener client in `frontend/src/services/telemetry.ts` that connects to the SSE/WebSocket endpoint.
- [ ] Ensure that incoming telemetry updates are directly piped into the Zustand store (`metricsSlice.ts`).
- [ ] Implement connection retry/recovery logic and error boundary displays when the Gateway connection is dropped.

## Dependencies
- `sub/frontend_zustand_store.md`

## Definition of Done
- Gateway exposes a telemetry streaming endpoint.
- Frontend establishes a connection and updates Zustand store in real-time.
- VRAM and CPU usage dashboards update smoothly without UI polling.
