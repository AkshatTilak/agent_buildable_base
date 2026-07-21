# Subtask: Docker Inference CPU Fallback Mode

**Parent Link:** `base/07_docker_infrastructure_ram.md`

## Actionable Steps
- [x] Remove mandatory GPU device reservation from `inference` service in `infrastructure/docker-compose.yml`.
- [x] Make GPU deployment conditional or move GPU config to `docker-compose.gpu.yml` override file.
- [x] Update `inference/main.py` and `vram_manager.py` to auto-detect CPU vs GPU availability:
  - If CUDA / GPU is unavailable, log a warning and fall back to CPU execution without throwing runtime errors.
  - Report `gpu_available: false` in `GET /v1/health`.
- [x] Ensure non-GPU machines (like 16GB host systems) can launch the inference container smoothly.

## Dependencies
- None.

## Definition of Done
- `docker compose up` starts inference container cleanly on non-GPU systems.
- Inference server API returns `gpu_available: false` gracefully when CUDA is missing.
