# Subtask: Shutdown Sequence

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [x] Stop accepting new requests (graceful drain with configurable timeout).
- [x] Call `shutdown_app_state()` for each active project.
- [x] Close inference client connections.
- [x] Commit/flush Kafka producer buffers.
- [x] Close database connections (Postgres, Neo4j, Redis sessions).
- [x] Log shutdown confirmation.

## Dependencies
- FastAPI lifespan management. All database and service clients.

## Definition of Done
- Gateway shuts down cleanly without resource leaks. All connections closed.
