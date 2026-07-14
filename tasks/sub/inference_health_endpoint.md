# Subtask: Inference Health Endpoint

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Expose `/health` endpoint returning detailed JSON system state.
- [x] Output structure includes status, loaded models with VRAM footprints, total VRAM budget, device details, and registered models.

## Dependencies
- VRAM Manager, FastAPI application setup.

## Definition of Done
- Calling `GET /health` returns a complete health status matching the defined schema contract.
