# ST02_02: Implement SQLAlchemy ORM Models

## Parent Link
`base/BT02_database_architecture.md`

## Actionable Steps
- [ ] Step 1: Create SQLAlchemy Base declarative class with async support
- [ ] Step 2: Implement Role model (id, role_name, permissions JSONB)
- [ ] Step 3: Implement User model (UUID id, email, password_hash, role_id FK)
- [ ] Step 4: Implement UserProfile model (user_id FK, preferences JSONB, usage_tier)
- [ ] Step 5: Implement UserAnalytics model (user_id FK, total_interactions, top_topics JSONB)
- [ ] Step 6: Implement Document model (UUID id, user_id FK, file_name, status enum)
- [ ] Step 7: Implement VectorKnowledge model with pgvector Vector column
- [ ] Step 8: Create corresponding Pydantic schemas for all models

## Dependencies
- `pgvector` Python package installed
- `sqlalchemy[asyncio]`, `asyncpg` installed

## Definition of Done
All ORM models defined with strict typing, relationships, and corresponding Pydantic schemas. Models align exactly with init_db.sql schema.
