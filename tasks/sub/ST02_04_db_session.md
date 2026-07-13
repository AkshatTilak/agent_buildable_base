# ST02_04: Implement Async Database Session

## Parent Link
`base/BT02_database_architecture.md`

## Actionable Steps
- [ ] Step 1: Create async SQLAlchemy engine with connection pooling (pool_size=20, max_overflow=10)
- [ ] Step 2: Create async_sessionmaker factory with expire_on_commit=False
- [ ] Step 3: Implement `get_db()` FastAPI dependency with commit/rollback handling
- [ ] Step 4: Wire into FastAPI lifespan for startup/shutdown
- [ ] Step 5: Write integration test verifying DB session lifecycle

## Dependencies
- PostgreSQL running and accessible
- `sqlalchemy[asyncio]`, `asyncpg` installed

## Definition of Done
Async session factory operational, FastAPI dependency injection working, connection pooling verified.
