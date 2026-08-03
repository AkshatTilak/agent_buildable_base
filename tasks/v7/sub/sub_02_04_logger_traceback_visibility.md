# Subtask 02_04: Gateway Exception Logging & Traceback Visibility

## Tasks
1. Update `common/observability/logger.py` and exception handlers in `gateway/main.py`.
2. Ensure unhandled HTTP 500 exceptions log complete tracebacks and request details to log files (`gateway.log`).
3. Ensure trace IDs are injected into error responses and correlated with backend log entries.
