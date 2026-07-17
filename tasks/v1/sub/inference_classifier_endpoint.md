# Subtask: Inference Classifier Endpoint

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [ ] Implement `/infer/classify` endpoint on inference server.
- [ ] Default model: `Arch-Router-1.5B` (GGUF, ~1-2 GB VRAM).
- [ ] Load via `llama-cpp-python` framework, integrated with VRAM Manager.
- [ ] Alternative: Semantic Router (zero VRAM, uses active embedding model for cosine-similarity routing).
- [ ] Input: `{"prompt": "User query text"}`.
- [ ] Output: `{"complexity": "simple|medium|complex", "required_agents": [...], "confidence": 0.92}`.
- [ ] Register with VRAM Manager lazy loading system.

## Dependencies
- `llama-cpp-python`, VRAM Manager, Model Registry.

## Definition of Done
- Endpoint returns valid classification JSON for arbitrary prompts.
- Model loads on demand and evicts after idle timeout.
- Semantic Router alternative works when configured.
