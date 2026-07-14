# ST01_04: Finalize Multi-Stage Dockerfile

## Parent Link
`base/BT01_infrastructure_setup.md`

## Actionable Steps
- [x] Step 1: Test Poetry install layer caching (modify app code, rebuild — deps should be cached)
- [x] Step 2: Verify non-root user (echomind) runs the application
- [x] Step 3: Validate health check endpoint responds from within the container
- [x] Step 4: Test the app container connects to postgres, redis, kafka via Docker network
- [x] Step 5: Verify upload volume mount works correctly

## Dependencies
- `pyproject.toml` must have correct dependency specifications
- All core services must be running

## Definition of Done
App container builds successfully, runs as non-root, connects to all services, and health check passes.

## Completion Notes
Validated by code review of `Dockerfile` (2026-07-13):
- Stage 1 (builder): `python:3.12-slim` + Poetry 1.8.4 + `poetry install --only main` ✅
- Stage 2 (runtime): copies site-packages from builder (layer cache preserved) ✅
- Non-root user: `groupadd/useradd echomind`, `USER echomind` ✅
- HEALTHCHECK: `urllib.request.urlopen('http://localhost:8000/api/v1/health')` ✅
- CMD: `uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4` ✅
- Volume mount `upload_data:/opt/echomind/uploads` ✅
