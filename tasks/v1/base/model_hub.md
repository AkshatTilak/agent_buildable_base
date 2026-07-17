# Base Task: Model Hub — HuggingFace Integration & Model Management

## Objective
Implement the HuggingFace Hub integration layer, quantization support, and device auto-detection to enable seamless local model loading across all inference endpoints.

## Business/System Value
Without this, all local models require manual downloading and configuration. This base task enables the "plug-and-play" model experience where switching to a new model is a single env var change.

## Subtask Registry
- [x] `sub/model_hub_registry_storage.md`
- [x] `sub/model_hub_env_override.md`
- [x] `sub/model_hub_registry_api.md`
- [x] `sub/model_hub_dynamic_vector_dim.md`
- [x] `sub/model_hub_auto_download.md`
- [x] `sub/model_hub_quantization.md`
- [x] `sub/model_hub_device_mapping.md`
- [x] `sub/model_hub_ocr_contracts.md`
- [x] `sub/model_hub_asr_contracts.md`
- [x] `sub/model_hub_embedding_contracts.md`
- [x] `sub/model_hub_classifier_contracts.md`
- [x] `sub/model_hub_completion_providers.md`

## Complexity Rating
**High** — Involves GPU memory management, multiple ML frameworks, and cross-model interface unification.

## References
- `references/logic/model_registry.md` — Full model catalog and specs.
- `references/logic/inference.md` — VRAM manager architecture.
