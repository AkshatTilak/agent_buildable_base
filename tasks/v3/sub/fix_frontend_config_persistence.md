# Subtask: Fix Frontend Config Modal Persistence

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [ ] In `App.tsx`, replace `defaultValue` inputs in the config modal with controlled `useState` values.
- [ ] Create a `settingsSlice.ts` in the Zustand store with `gatewayUrl` and `apiKey` fields.
- [ ] On "Save & Close", persist values to both Zustand store and `localStorage`.
- [ ] On app load, initialize settings from `localStorage` (with fallback defaults).
- [ ] Update `api.ts` to read `gatewayUrl` and `apiKey` from the settings store instead of hardcoding.
- [ ] Update `telemetry.ts` to read gateway host/port from the settings store instead of hardcoding port 8000.
- [ ] Add a "Reset to Defaults" button in the settings modal.

## Dependencies
- None (can be done independently).

## Definition of Done
- Config modal persists values across page refreshes.
- API client and telemetry service use stored gateway URL and API key.
- `localStorage` key `contained-settings` holds serialized settings.
