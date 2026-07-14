# Subtask: Inference Device Auto-Detection

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Read `DEVICE` setting (`auto`, `cuda`, `cpu`).
- [x] If `auto`: detect CUDA; fall back to CPU.
- [x] If `cpu`: load models on CPU.

## Dependencies
- Configuration settings, PyTorch/CUDA environment.

## Definition of Done
- System starts and maps loads to the correct device matching the configuration.
