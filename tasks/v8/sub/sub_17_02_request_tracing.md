# Subtask 17_02: Request Tracing for Tests

## Parent Base Task
`tasks/v8/base/17_test_observability_logging.md`

## Objective
Inject a `X-Test-Trace-ID` header into every test HTTP request that correlates with gateway, DB, and streaming logs.

## Tasks
1. [ ] Inject `X-Test-Trace-ID` header into every test HTTP request.
2. [ ] Correlate with gateway's `RequestIdMiddleware` trace ID.
3. [ ] Correlate with DB query logs.
4. [ ] Correlate with streaming event logs.
5. [ ] Enable tracing a test failure through the entire backend stack.

## Definition of Done
- `X-Test-Trace-ID` correlates HTTP, DB, and streaming logs.
- Test failures can be traced through the entire backend stack.
