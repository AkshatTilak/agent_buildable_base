# Subtask 01_03: Gateway/Inference Process Management & Structured Test Logging

## Parent Base Task
`tasks/v8/base/01_test_infrastructure_foundation.md`

## Objective
Create session-scoped process management and fixtures for the gateway (`:8000`) and inference (`:8001`) servers on actual standard ports, plus structured test logging and pytest config updates.

## Tasks
1. [x] Create session-scoped async fixtures / process management that:
   - Target gateway via `poetry run uvicorn gateway.main:app --port 8000` (or running dev gateway).
   - Target inference via `poetry run uvicorn inference.main:app --port 8001` (or running dev inference).
   - Wait for health checks to pass before yielding.
2. [x] Configure pytest JSON reporter + custom log formatter capturing:
   - Test name, marker, duration.
   - All HTTP requests/responses (URL, method, status, latency).
   - DB queries executed (count, slow queries >100ms).
   - Assertion failures with full context.
   - Streaming events received/sent count.
3. [x] Update `pytest.ini` / `pyproject.toml`:
   - Register custom markers.
   - Configure `testpaths` to include new directories.
   - Set default `--strict-markers`.

## Definition of Done
- Gateway/Inference fixtures verify health on standard ports (`:8000`, `:8001`).
- Structured JSON logs written per test to `tests/logs/{run_timestamp}/`.
- Markers registered and `--strict-markers` enforced.
