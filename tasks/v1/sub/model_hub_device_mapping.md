# Subtask: Device Map Auto-Detection

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] `DEVICE=auto` (default): auto-detect CUDA availability; fall back to CPU.
- [ ] `DEVICE=cuda`: force GPU (fail with clear error if unavailable).
- [ ] `DEVICE=cpu`: force CPU (degraded performance warning for local models).
- [ ] Pass device setting through to all model loading functions in the inference server.
- [ ] Log selected device on startup.

## Dependencies
- `torch` package with CUDA support in inference extras.
- `DEVICE` setting in `common/config/settings.py`.

## Definition of Done
- All three device modes work correctly (`auto`, `cuda`, `cpu`).
- Models load on the correct device.
- Clear errors when forcing `cuda` without GPU.
- Startup log confirms detected/forced device.
