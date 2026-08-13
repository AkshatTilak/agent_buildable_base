# Subtask 01_03: Gateway/Inference Process Fixtures & Structured Test Logging

## Parent Base Task
`tasks/v8/base/01_test_infrastructure_foundation.md`

## Objective
Create session-scoped subprocess fixtures for the gateway and inference servers, plus structured test logging and pytest config updates.

## Tasks
1. [x] Create session-scoped async fixtures that:
   - Start gateway via `poetry run uvicorn gateway.main:app --port 8100`.
   - Start inference via `poetry run uvicorn inference.main:app --port 8110`.
   - Wait for health checks to pass before yielding.
   - Kill processes on teardown.
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
- Gateway/Inference subprocess fixtures start, pass health checks, and are killed on teardown.
- Structured JSON logs written per test to `tests/logs/{run_timestamp}/`.
- Markers registered and `--strict-markers` enforced.
