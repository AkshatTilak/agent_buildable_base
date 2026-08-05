# Subtask 08_04: Local Disk Storage Path, Model Purging & Harrier Inference Integration

## Objective
Implement local model disk storage path resolution, disk existence status checking (`is_downloaded`), model deletion with optional disk purging (`DELETE /api/models/local/{model_id}?purge_disk=true`), HuggingFace credential support (`HF_TOKEN`), and Harrier 0.6B local embedder loader integration in GPU Inference Server.

## Tasks
1. `[x]` Implement `resolve_local_model_disk_info()` in `gateway/api/models.py` to calculate HuggingFace Hub cache paths (`HF_HOME`) and GGUF workspace paths (`./models/*.gguf`).
2. `[x]` Extend `GET /api/models/local/status` to return `local_path` and `is_downloaded` for all registered local models.
3. `[x]` Implement `DELETE /api/models/local/{model_id:path}` endpoint in `gateway/api/models.py` with `purge_disk=true` parameter to unload from VRAM, delete local cache folders (`shutil.rmtree`), and remove DB registry entries.
4. `[x]` Integrate `Harrier 0.6B` local embedding model loader in `inference/models/harrier.py` and register in `inference/main.py`.
5. `[x]` Add `huggingface` (`HF_TOKEN`, `HUGGINGFACE_API_KEY`, `HF_API_KEY`) to `ENV_PROVIDER_MAP` in `gateway/api/credentials.py`.
6. `[x]` Add Disk Storage Path display, cache status badges, and Delete Model confirmation modal in `LocalModelsPage.tsx`.
7. `[x]` Add unit test suite in `tests/test_delete_local_model.py`.
