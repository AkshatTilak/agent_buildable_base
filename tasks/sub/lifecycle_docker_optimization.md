# Subtask: Docker Image Optimization

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [ ] Use multi-stage builds to reduce final image size.
- [ ] Pin base image versions (avoid `latest` tags in production).
- [ ] Run containers as non-root user.

## Dependencies
- Existing Dockerfiles (Dockerfile.gateway, Dockerfile.inference).

## Definition of Done
- Images smaller. No `latest` tags in production Dockerfiles. Non-root execution.
