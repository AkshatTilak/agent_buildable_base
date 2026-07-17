# Subtask: Inference Model Registry Integration

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Read enabled local models from the Model Registry on startup.
- [x] Register loader functions only for models marked as `is_enabled=True` and `mode='local'`.
- [x] Support hot-reload of registry settings without server restart.

## Dependencies
- Model Registry API (`common/models/registry.py`).

## Definition of Done
- Inference loader mapping dynamically mirrors the state of the active model registry.
