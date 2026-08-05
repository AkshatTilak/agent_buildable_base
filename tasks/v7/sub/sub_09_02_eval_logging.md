# Subtask 09_02: EvalOps Error Boundaries & Trace Logging

## Objective
Wrap evaluation metric calculations in dedicated exception boundaries, log detailed tracebacks to `evalops.log`, and return itemized test status.

## Tasks
1. `[x]` Implement error boundary wrapper around DeepEval metric calculations in `projects/evalops/metrics/runner.py`.
2. `[x]` Log unhandled metric exceptions with complete traceback to `logs/evalops.log`.
3. `[x]` Ensure individual test case failures set status `FAILED` with error reason without aborting the rest of the evaluation suite.
4. `[x]` Add unit tests in `tests/test_eval_logging.py`.
