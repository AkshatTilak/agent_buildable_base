# Subtask: HuggingFace Auto-Download on First Use

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [x] When a local model is selected but weights are not cached, auto-download from HuggingFace Hub using `huggingface_hub.snapshot_download()` or `transformers.AutoModel.from_pretrained()`.
- [x] Display download progress in logs (structured JSON log entries with percentage).
- [x] Support gated models with `HF_TOKEN` authentication.
- [x] Read `HF_HOME` from settings for cache root directory.
- [x] Read `HF_HUB_ENABLE_HF_TRANSFER=1` for faster downloads.
- [x] Support `HF_HUB_OFFLINE=1` for air-gapped environments (skip download, fail if not cached).
- [x] Support `MODEL_CACHE_DIR` for custom cache paths (GGUF and non-HF files).

## Implementation Notes (V8)
- `MODEL_CACHE_DIR` is now a first-class setting in `common/config/settings.py` (default `./models`), read by `inference/core/downloader.py` and `gateway/api/models.py::resolve_local_model_disk_info`.
- The VRAM pre-download path (`inference/core/vram_manager.py`) maps local model IDs to their HuggingFace repos and downloads into `MODEL_CACHE_DIR` on first load, so the workspace `models/` folder populates automatically.
- `models/` is gitignored (weights are not committed).

## Dependencies
- `huggingface-hub` package in `pyproject.toml` base dependencies.
- `HF_HOME`, `HF_TOKEN`, `HF_HUB_ENABLE_HF_TRANSFER`, `HF_HUB_OFFLINE` env vars configured.
- VRAM Manager's model loading pathway (`inference/vram_manager.py`).

## Definition of Done
- Local models auto-download on first inference request.
- Download progress is visible in structured logs.
- Gated models work with valid `HF_TOKEN`.
- Air-gapped mode (`HF_HUB_OFFLINE=1`) fails gracefully if model is not cached.
