# ST02_02: Implement SQLAlchemy ORM Models

## Parent Link
`base/BT02_database_architecture.md`

## Actionable Steps
- [x] Step 1: Create SQLAlchemy Base declarative class with async support
- [x] Step 2: Implement Role model (id, role_name, permissions JSONB)
- [x] Step 3: Implement User model (UUID id, email, password_hash, role_id FK)
- [x] Step 4: Implement UserProfile model (user_id FK, preferences JSONB, usage_tier)
- [x] Step 5: Implement UserAnalytics model (user_id FK, total_interactions, top_topics JSONB)
- [x] Step 6: Implement Document model (UUID id, user_id FK, file_name, status enum)
- [x] Step 7: Implement VectorKnowledge model with pgvector Vector column
- [x] Step 8: Create corresponding Pydantic schemas for all models

## Dependencies
- `pgvector` Python package installed
- `sqlalchemy[asyncio]`, `asyncpg` installed

## Definition of Done
All ORM models defined with strict typing, relationships, and corresponding Pydantic schemas. Models align exactly with init_db.sql schema.

## Completion Notes
Implemented 2026-07-14:
- `Base` in `app/db/session.py` (shared declarative base) ✅
- `app/models/user.py`: Role, User, UserProfile, UserAnalytics + Pydantic schemas ✅
- `app/models/document.py`: DocumentStatus enum, Document ORM + Pydantic schemas ✅
- `app/models/vector.py`: VectorKnowledge with `pgvector.sqlalchemy.Vector(3072)` ✅
- `app/models/__init__.py`: re-exports all models for Alembic discovery ✅
- All relationships defined with `back_populates` and cascade delete ✅
- Strict Python typing with `Mapped[...]` on all columns ✅
