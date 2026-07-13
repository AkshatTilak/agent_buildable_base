# BT02: Database Architecture & Vector Storage

## Objective
Implement the full PostgreSQL relational schema with pgvector extension, HNSW indexing, and Alembic migration support.

## Business/System Value
All downstream systems (auth, agent, RAG, analytics) depend on a correctly structured database with high-performance vector search capabilities.

## Subtask Registry
- [ ] `sub/ST02_01_init_db_sql.md` — Finalize and test init_db.sql (tables, indexes, seed data)
- [ ] `sub/ST02_02_sqlalchemy_models.md` — Implement SQLAlchemy ORM models (User, Role, Document, VectorKnowledge)
- [ ] `sub/ST02_03_alembic_setup.md` — Configure Alembic for async SQLAlchemy migrations
- [ ] `sub/ST02_04_db_session.md` — Implement async session factory and FastAPI dependency

## Complexity Rating
**High** — Requires correct pgvector configuration, HNSW tuning, and strict schema alignment.

## Source Code Location
`../../echomind-core/app/db/`, `../../echomind-core/app/models/`
