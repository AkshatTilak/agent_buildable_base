# Subtask 10_02: Inference Proxy Live Tests

## Parent Base Task
`tasks/v8/base/10_live_api_tests.md`

## Objective
Create `tests/live_api/test_inference_proxy.py` — OCR & embedding endpoints.

## Tasks
1. [x] Proxy request through gateway to inference server (if running).
2. [x] Verify OCR endpoint with a real image.
3. [x] Verify embedding endpoint with real text.

## Definition of Done
- Inference proxy OCR/embedding endpoints verified against actual running Inference server (`:8010`) and Gateway (`:8000`).
- Proxy request through gateway to inference server verified.
- If errors arise, inspect Docker container logs and fix root causes directly in inference routing.
- Deleting test data is not necessary.
