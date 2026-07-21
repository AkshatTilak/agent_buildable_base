# Subtask: Telemetry Service Settings Sync & History Buffer

**Parent Link:** `base/05_frontend_state_data_layer.md`

## Actionable Steps
- [ ] `telemetry.ts` — Replace hardcoded `host:8000` WebSocket URL with dynamic value from `useStore.getState().gatewayUrl`.
- [ ] `telemetry.ts` — Replace hardcoded SSE URL similarly.
- [ ] Add telemetry history buffer to `metricsSlice.ts`:
  - `telemetryHistory: TelemetryMetrics[]` (max 60 entries — last 60 seconds of data)
  - On each `setTelemetry()` call, push to history array with `slice(-60)` to cap size.
  - This enables sparkline charts in SystemMetrics.
- [ ] Add reconnection attempt counter to display in UI (e.g., "Reconnecting... attempt 3").
- [ ] Change reconnection from fixed 5s interval to exponential backoff: 1s → 2s → 4s → 8s → max 30s.
- [ ] Add `lastConnectedAt: number | null` timestamp to metricsSlice for "last updated" display.

## Dependencies
- `sub/state_new_zustand_slices.md` (settingsSlice must exist).

## Definition of Done
- Telemetry reads gateway URL from settings store.
- History buffer contains last 60 data points.
- Reconnection uses exponential backoff.
