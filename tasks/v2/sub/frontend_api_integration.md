# Subtask: Frontend API Integration

**Parent Link:** `base/01_frontend_granular_realtime.md`

## Actionable Steps
- [ ] Implement an API client layer in `frontend/src/services/api.ts` using `axios` or native `fetch`.
- [ ] Connect the ingestion configuration options to `/api/syntraflow/ingest` endpoint.
- [ ] Connect the Agent Hub forms and views to the new `/api/agents` CRUD endpoints.
- [ ] Connect the Workflow Builder to the workflow save/load and execution API endpoints.
- [ ] Connect the Evaluation Panel to trigger, list, and view results from `/api/evals`.
- [ ] Connect Model Registry dropdowns to fetch live available models using `/api/models` registry endpoint.

## Dependencies
- `sub/frontend_component_breakdown.md`

## Definition of Done
- All REST calls are centralized in a clean, typed API service file.
- Error handling matches standard API error response structures, showing human-readable alerts to the user.
