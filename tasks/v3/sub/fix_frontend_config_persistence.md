# Subtask: Fix Frontend Config Modal Persistence

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [x] In `App.tsx`, replace `defaultValue` inputs in the config modal with controlled `useState` values.
- [x] Create a `settingsSlice.ts` in the Zustand store with `gatewayUrl` and `apiKey` fields.
- [x] On "Save & Close", persist values to both Zustand store and `localStorage`.
- [x] On app load, initialize settings from `localStorage` (with fallback defaults).
- [x] Update `api.ts` to read `gatewayUrl` and `apiKey` from the settings store instead of hardcoding.
- [x] Update `telemetry.ts` to read gateway host/port from the settings store instead of hardcoding port 8000.
- [x] Add a "Reset to Defaults" button in the settings modal.

## Dependencies
- None (can be done independently).

## Definition of Done
- Config modal persists values across page refreshes.
- API client and telemetry service use stored gateway URL and API key.
- `localStorage` key `contained-settings` holds serialized settings.
