# Subtask: Create New Zustand Store Slices

**Parent Link:** `base/05_frontend_state_data_layer.md`

## Actionable Steps
- [x] Create `frontend/src/store/settingsSlice.ts`:
  - `gatewayUrl: string` (default: `"http://localhost:8000"`)
  - `apiKey: string` (default: `"sk_live_default_key"`)
  - `sidebarCollapsed: boolean` (default: `false`)
  - `setGatewayUrl`, `setApiKey`, `toggleSidebar` actions
  - On any change, persist to `localStorage` key `contained-settings`
  - On store creation, initialize from `localStorage` if available
- [x] Create `frontend/src/store/notificationSlice.ts`:
  - `notifications: Notification[]` (array of `{ id, type, title, message, read, timestamp }`)
  - `unreadCount: number` (computed)
  - `addNotification`, `markRead`, `markAllRead`, `clearAll` actions
- [x] Create `frontend/src/store/ingestionSlice.ts`:
  - `activeJobs: IngestionJob[]` (tracked active/recent jobs)
  - `uploadProgress: number | null` (current upload percentage)
  - `addJob`, `updateJob`, `setUploadProgress`, `clearJobs` actions
- [x] Update `frontend/src/store/useStore.ts`:
  - Import and combine all new slices
  - Update `StoreState` type to include all new slice types
- [x] Add `zustand/middleware` `persist` for settingsSlice localStorage integration.

## Dependencies
- `sub/state_typescript_interfaces.md` (for typed interfaces).

## Definition of Done
- Three new Zustand slices created and integrated.
- Settings persist to localStorage across page refreshes.
- Notifications can be added and read from any component.
