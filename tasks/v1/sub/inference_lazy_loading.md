# Subtask: Inference VRAM On-Demand Lazy Loading

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Keep model weights unloaded from VRAM until an incoming request targets a specific endpoint.
- [x] Automatically load the model at request time and update its last-used timestamp.

## Dependencies
- VRAM Manager (`inference/vram_manager.py`).

## Definition of Done
- Models do not load into VRAM on startup; they load only when targeted by an active inference query.
