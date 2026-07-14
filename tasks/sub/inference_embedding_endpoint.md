# Subtask: Inference Embedding Endpoint

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [ ] Implement `/infer/embed` endpoint on inference server.
- [ ] Default model: `jinaai/jina-clip-v2` (~1 GB VRAM). Framework: `sentence-transformers`.
- [ ] Provide unified embedding space for both text and image inputs.
- [ ] Native output: **1024** elements (NOT 768).
- [ ] Support Matryoshka dimension truncation.
- [ ] Vector dimension must match Qdrant collection configuration.
- [ ] Cloud alternative: Gemini Embedding 2 (up to 3072 dim).
- [ ] Register with VRAM Manager lazy loading system.

## Dependencies
- `sentence-transformers` or `transformers`/`torch` packages.
- VRAM Manager, Model Registry, Qdrant client.

## Definition of Done
- Endpoint produces embeddings for text and image inputs.
- Vector dimension is 1024 (jina-clip-v2) and matches Qdrant.
- Matryoshka truncation works.
