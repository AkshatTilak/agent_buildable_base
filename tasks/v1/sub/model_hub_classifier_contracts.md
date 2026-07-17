# Subtask: Classifier Model Contracts & Adapters

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] Implement unified classifier adapter interface.
- [ ] Support local: Arch-Router-1.5B, Qwen3-4B, Phi-4-Mini, Gemma 3 4B (all GGUF).
- [ ] Support zero-VRAM: Semantic Router (cosine similarity via embedding model).
- [ ] Support cloud: Gemini 3.5 Flash (structured JSON output).
- [ ] Implement rule-based fallback (regex/keyword matching) — always available.
- [ ] Standardized JSON output:
  ```json
  {"complexity": "simple|medium|complex", "required_agents": [...], "confidence": 0.92}
  ```

## Dependencies
- `llama-cpp-python` for GGUF models.
- Active embedding model for Semantic Router.

## Definition of Done
- Each classifier selectable via `CLASSIFIER_MODEL` env var.
- Semantic Router works without loading a separate model.
- Rule-based fallback engages automatically on classifier failure.
