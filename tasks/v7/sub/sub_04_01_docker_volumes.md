# Subtask: Docker Volumes to Local Host Path

- Update `infrastructure/docker-compose.yml` so that persistent volumes (like postgres data, qdrant data) are mapped to `./data/...` instead of named Docker volumes.
- This ensures consistency between running local bare-metal vs Docker.
