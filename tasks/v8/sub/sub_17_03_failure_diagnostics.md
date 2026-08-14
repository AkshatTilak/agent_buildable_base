# Subtask 17_03: Test Failure Diagnostics

## Parent Base Task
`tasks/v8/base/17_test_observability_logging.md`

## Objective
On test failure, automatically capture and attach diagnostics.

## Tasks
1. [ ] Capture last N HTTP request/response pairs.
2. [ ] Capture current DB state for relevant tables (e.g., if hub test fails, dump hub + member rows).
3. [ ] Capture gateway logs from the trace window.
4. [ ] Capture Redis cache state (if caching tests).
5. [ ] Capture Docker container logs (`docker compose logs <service>`) for relevant infrastructure containers.

## Definition of Done
- On failure, HTTP pairs, DB state, gateway logs, Redis state, and Docker container logs are captured.
- Deleting test data is not necessary.
