# Subtask: Shutdown Sequence

**Parent Link:** `base/system_lifecycle.md`

## Actionable Steps
- [ ] Stop accepting new requests (graceful drain with configurable timeout).
- [ ] Call `shutdown_app_state()` for each active project.
- [ ] Close inference client connections.
- [ ] Commit/flush Kafka producer buffers.
- [ ] Close database connections (Postgres, Neo4j, Redis sessions).
- [ ] Log shutdown confirmation.

## Dependencies
- FastAPI lifespan management. All database and service clients.

## Definition of Done
- Gateway shuts down cleanly without resource leaks. All connections closed.
