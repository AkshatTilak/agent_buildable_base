# Subtask: Inference VRAM Idle Cleanup Daemon

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Run a background task that checks model status every 60 seconds.
- [x] Unload models from VRAM if they remain inactive for more than `CLASSIFIER_IDLE_TIMEOUT` seconds.

## Dependencies
- VRAM Manager (`inference/vram_manager.py`).

## Definition of Done
- Background cleanup loop successfully runs and unloads models after their configured idle timeout threshold.
