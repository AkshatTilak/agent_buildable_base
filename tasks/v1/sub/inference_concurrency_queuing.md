# Subtask: Inference Concurrency & Queuing

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [x] Implement request queuing: second request waits for model load using `asyncio.Lock` in VRAM Manager.
- [x] Allow concurrent inference calls on a model that is already loaded in memory (no global inference lock).
- [x] Optionally enforce per-model concurrency limits to prevent memory spikes.

## Dependencies
- VRAM Manager, asyncio framework.

## Definition of Done
- Multiple concurrent requests do not corrupt the VRAM state or trigger parallel weight loading loops.
