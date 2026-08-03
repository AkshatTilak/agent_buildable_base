# Subtask 02_02: Remove Silent Fallbacks Across Frontend & Gateway

## Tasks
1. Remove `FALLBACK_SYSTEM_HEALTH` in `frontend/src/App.tsx`. Show explicit error banner or status indicator when system data fetch fails.
2. Audit all API handlers in `frontend/src/services/api.ts` to ensure network or server errors throw instead of returning dummy data.
3. Audit `gateway/api/ingestion_hub.py`, `agent_crud.py`, `workflows.py`, `eval_hub.py` to remove silent fallback data generation when backend engines fail.
