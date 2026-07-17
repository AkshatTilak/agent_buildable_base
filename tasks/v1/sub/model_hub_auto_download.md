# Subtask: HuggingFace Auto-Download on First Use

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] When a local model is selected but weights are not cached, auto-download from HuggingFace Hub using `huggingface_hub.snapshot_download()` or `transformers.AutoModel.from_pretrained()`.
- [ ] Display download progress in logs (structured JSON log entries with percentage).
- [ ] Support gated models with `HF_TOKEN` authentication.
- [ ] Read `HF_HOME` from settings for cache root directory.
- [ ] Read `HF_HUB_ENABLE_HF_TRANSFER=1` for faster downloads.
- [ ] Support `HF_HUB_OFFLINE=1` for air-gapped environments (skip download, fail if not cached).
- [ ] Support `MODEL_CACHE_DIR` for custom cache paths (GGUF and non-HF files).

## Dependencies
- `huggingface-hub` package in `pyproject.toml` base dependencies.
- `HF_HOME`, `HF_TOKEN`, `HF_HUB_ENABLE_HF_TRANSFER`, `HF_HUB_OFFLINE` env vars configured.
- VRAM Manager's model loading pathway (`inference/vram_manager.py`).

## Definition of Done
- Local models auto-download on first inference request.
- Download progress is visible in structured logs.
- Gated models work with valid `HF_TOKEN`.
- Air-gapped mode (`HF_HUB_OFFLINE=1`) fails gracefully if model is not cached.
