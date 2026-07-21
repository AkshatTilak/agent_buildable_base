# Base Task: Frontend State Management & Data Layer Hardening

## Objective
Restructure the Zustand store with new domain slices (settings, notifications, ingestion jobs), eliminate all `any` types from the API client, implement localStorage persistence for user settings, and add proper TypeScript interfaces throughout the service layer.

## Business/System Value
V2's data layer has multiple quality gaps: the API client uses `any` for every response type, telemetry service hardcodes the gateway port, there's no settings persistence, and no notification system. V3 hardens this into a type-safe, persistent, notification-capable state management system.

## Complexity Rating
Medium (TypeScript refactoring + new Zustand slices + localStorage integration).

## Subtask Registry
* `[x] sub/state_typescript_interfaces.md` — Create `frontend/src/types/` directory with proper TypeScript interfaces for all API responses.
* `[ ] sub/state_new_zustand_slices.md` — Create settingsSlice, notificationSlice, and ingestionSlice.
* `[ ] sub/state_api_client_hardening.md` — Replace all `any` in api.ts with typed interfaces, read settings from store, add retry logic.
* `[ ] sub/state_telemetry_settings_sync.md` — Telemetry service reads gateway URL from settings store; add history buffer for sparklines.
