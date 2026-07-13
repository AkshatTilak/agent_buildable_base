# ST02_01: Finalize and Test init_db.sql

## Parent Link
`base/BT02_database_architecture.md`

## Actionable Steps
- [ ] Step 1: Verify pgvector extension installs correctly on pgvector/pgvector:pg16
- [ ] Step 2: Validate all 6 tables are created (roles, users, user_profiles, user_analytics, documents, vector_knowledge)
- [ ] Step 3: Verify default roles are seeded (standard, premium, admin) with correct JSONB permissions
- [ ] Step 4: Test HNSW index creation on vector_knowledge.embedding
- [ ] Step 5: Validate all foreign key constraints and cascading deletes

## Dependencies
- PostgreSQL container running

## Definition of Done
All tables created, indexed, and seeded. Foreign key constraints verified with test data.
