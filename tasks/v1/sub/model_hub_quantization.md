# Subtask: Quantization Support

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] For `transformers`-based models: support `load_in_8bit` / `load_in_4bit` via `bitsandbytes`.
- [ ] For GGUF models: support Q4_K_M, Q5_K_M, Q8_0 quantization levels via `llama-cpp-python`.
- [ ] Registry entry specifies the quantization level per model (field: `quantization`).
- [ ] VRAM Manager reads quantization setting from ModelSpec when loading a model.
- [ ] Validate that the quantization method is compatible with the model's framework.

## Dependencies
- `bitsandbytes` package (for transformers quantization).
- `llama-cpp-python` package (for GGUF loading).
- Model Registry `quantization` field populated.

## Definition of Done
- Models load with correct quantization as specified in their registry entry.
- VRAM usage matches the expected budget for each quantization level.
- Incompatible quantization settings raise clear errors.
