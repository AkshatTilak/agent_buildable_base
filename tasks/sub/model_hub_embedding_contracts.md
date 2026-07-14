# Subtask: Embedding Model Contracts & Adapters

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] Implement unified embedding adapter interface.
- [ ] Support local: jina-clip-v2, nomic-embed-vision-v2, Qwen3-Embedding-0.6B.
- [ ] Support cloud: Gemini Embedding 2, Voyage Multimodal 3, OpenAI text-embedding-3-large.
- [ ] Enforce vector dimension rules: Qdrant collection dimension matches active model.
- [ ] Support Matryoshka dimension truncation where available.
- [ ] Document supported truncation dimensions per model.

## Dependencies
- `sentence-transformers`, `transformers` packages.
- Qdrant client vector dimension configuration.

## Definition of Done
- Each embedding model selectable via `EMBEDDING_MODEL` env var.
- Vector dimensions correctly match Qdrant collection.
- Dimension mismatch detected and warned on startup.
