# Subtask: Frontend Project Setup

**Parent Link:** `base/frontend_ui.md`

## Actionable Steps
- [ ] Initialize React 18+ project with TypeScript using Vite in `frontend/` directory.
- [ ] Install key libraries: `@xyflow/react`, `recharts`, `@tanstack/react-query`, `react-markdown`, `react-syntax-highlighter`, `lucide-react`.
- [ ] Set up API client layer: REST + SSE. Auto-generate types from `/openapi.json`.
- [ ] Configure `VITE_API_URL` env var (default: `http://localhost:8000`).
- [ ] Add `frontend` service to `docker-compose.yml` (or serve via `fastapi.staticfiles`).

## Dependencies
- Node.js, npm/pnpm. Gateway running for API spec.

## Definition of Done
- Vite dev server starts. API client connects to gateway. Docker integration configured.
