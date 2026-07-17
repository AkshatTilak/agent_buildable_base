# Subtask: Inference Latency Tracking

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Trace and log cold-start latency (first load request) separately from warm query execution times.
- [x] Return these latency records in the diagnostics block of health and logging reports.

## Dependencies
- VRAM Manager, logging utilities.

## Definition of Done
- Console logs and health statistics accurately identify the cold-start cost separate from active model computation times.
