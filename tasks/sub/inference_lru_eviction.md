# Subtask: Inference VRAM LRU Eviction Scheduler

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Maintain a configurable VRAM memory budget (default: 20 GB).
- [x] If loading a model would exceed this limit, evict the Least Recently Used (LRU) model from memory before loading.

## Dependencies
- VRAM Manager (`inference/vram_manager.py`).

## Definition of Done
- Exceeding the VRAM budget triggers eviction of inactive models until enough memory is freed to accommodate the requested model.
