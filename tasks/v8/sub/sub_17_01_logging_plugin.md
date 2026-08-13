# Subtask 17_01: Test Logging Plugin

## Parent Base Task
`tasks/v8/base/17_test_observability_logging.md`

## Objective
Create a custom pytest logging plugin in `tests/conftest.py` that captures HTTP/DB activity and writes structured JSON logs.

## Tasks
1. [ ] Capture all `httpx` / `TestClient` requests with full URL, method, status code, response time, and body size.
2. [ ] Capture all SQLAlchemy queries with execution time (via event hooks).
3. [ ] Write structured JSON logs per test to `tests/logs/{run_timestamp}/`.
4. [ ] Generate a summary report at end of session: total tests, pass/fail/skip, slowest tests, most DB-heavy tests, failed assertion details.

## Definition of Done
- Structured JSON logs written per test to `tests/logs/{run_timestamp}/`.
- Summary report generated at end of session.
