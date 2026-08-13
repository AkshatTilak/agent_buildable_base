# Base Task 17: Test Observability & Logging Infrastructure (v8)

## Objective
Build structured logging and observability into the test suite itself, so test runs produce actionable diagnostics — not just pass/fail.

## Why
Without structured logging and request tracing, test failures are hard to diagnose. This task makes it possible to trace a test failure through the entire backend stack (HTTP → DB → streaming → Redis).

## Scope
- **`tests/conftest.py` logging plugin** — Custom pytest plugin that:
  - Captures all `httpx` / `TestClient` requests with full URL, method, status code, response time, and body size.
  - Captures all SQLAlchemy queries with execution time (via event hooks).
  - Writes structured JSON logs per test to `tests/logs/{run_timestamp}/`.
  - Generates a summary report at end of session: total tests, pass/fail/skip, slowest tests, most DB-heavy tests, failed assertion details.
- **Request tracing for tests** — Inject a `X-Test-Trace-ID` header into every test HTTP request that correlates with:
  - Gateway's `RequestIdMiddleware` trace ID.
  - DB query logs.
  - Streaming event logs.
  - This makes it possible to trace a test failure through the entire backend stack.
- **Test failure diagnostics** — On test failure, automatically capture and attach:
  - Last N HTTP request/response pairs.
  - Current DB state for relevant tables (e.g., if hub test fails, dump hub + member rows).
  - Gateway logs from the trace window.
  - Redis cache state (if caching tests).
- **`pytest-html` integration** — Generate HTML test report with:
  - Collapsible per-test logs.
  - Embedded request/response timelines.
  - Screenshot/recording attachments for Playwright tests.
- **CI-friendly output** — JUnit XML output + JSON summary for CI pipeline integration.

## Associated Subtasks
1. `[x]` `sub_17_01_logging_plugin.md`: Custom pytest logging plugin (HTTP/DB capture, JSON logs, summary report).
2. `[x]` `sub_17_02_request_tracing.md`: `X-Test-Trace-ID` injection correlated with gateway/DB/streaming logs.
3. `[x]` `sub_17_03_failure_diagnostics.md`: On-failure capture of HTTP pairs, DB state, gateway logs, Redis state.
4. `[x]` `sub_17_04_reports_and_ci.md`: `pytest-html` report, JUnit XML, JSON summary.


## Definition of Done
- Structured JSON logs written per test to `tests/logs/{run_timestamp}/`.
- `X-Test-Trace-ID` correlates HTTP, DB, and streaming logs.
- On failure, HTTP pairs, DB state, gateway logs, and Redis state are captured.
- `pytest-html` report with collapsible logs and timelines generated.
- JUnit XML + JSON summary output for CI.
