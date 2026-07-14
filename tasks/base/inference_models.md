# Base Task: Inference Model Endpoints

## Objective
Implement the inference server endpoints for all four model roles: Task Classifier, ASR, Embedding, and OCR. Each endpoint must integrate with the VRAM Manager, Model Registry, and produce output matching the defined JSON schema contracts.

## Business/System Value
These are the GPU-bound AI workloads that power every feature in the platform. Without operational inference endpoints, SyntraFlow can't process documents, GuardRoute can't classify queries, and the platform falls back to cloud-only mode.

## Subtask Registry
- [ ] `sub/inference_classifier_endpoint.md`
- [ ] `sub/inference_asr_endpoint.md`
- [ ] `sub/inference_embedding_endpoint.md`
- [ ] `sub/inference_ocr_endpoint.md`

## Complexity Rating
**High** — Requires deep integration with multiple ML frameworks (`llama-cpp-python`, `funasr`, `sentence-transformers`, `transformers`, `paddleocr`) and the VRAM Manager's lazy-loading system.

## References
- `references/logic/inference.md` — Inference server architecture.
- `references/logic/model_registry.md` — Model options and JSON contracts.
