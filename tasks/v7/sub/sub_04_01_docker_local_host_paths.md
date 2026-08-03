# Subtask 04_01: Docker Volume Mounts to Local Host Paths

## Tasks
1. Modify `infrastructure/docker-compose.yml`.
2. Replace Docker named volume references (`postgres_data`, `qdrant_data`, `redis_data`, `neo4j_data`) with host-relative paths:
   - `./data/postgres:/var/lib/postgresql/data`
   - `./data/qdrant:/qdrant/storage`
   - `./data/redis:/data`
   - `./data/neo4j:/data`
3. Update `.gitignore` to ignore the top-level `./data/` folder.
4. Verify containers start cleanly and persist state to host directories.
