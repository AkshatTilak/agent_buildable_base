# Subtask: Dockerfile Inference

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Create `Dockerfile.inference` with GPU capability.
- [x] Base: `nvidia/cuda:12.4.1-runtime-ubuntu22.04`.
- [x] Install python, build tools, base + inference extras (torch, transformers, llama-cpp-python).
- [x] Copy inference modules and expose port 8010.

## Dependencies
- NVIDIA CUDA toolkit runtime image.

## Definition of Done
- Inference Docker image builds and starts the inference server on port 8010.
