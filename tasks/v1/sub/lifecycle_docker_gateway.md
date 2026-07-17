# Subtask: Dockerfile Gateway

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Create `Dockerfile.gateway` for CPU-only service.
- [x] Base: `python:3.11-slim`, install Poetry.
- [x] Install base + all project extras, including `ffmpeg` binary.
- [x] Copy source code and expose port 8000.

## Dependencies
- Poetry package manager, python base image.

## Definition of Done
- Gateway Docker image builds successfully and starts the FastAPI server on port 8000.
