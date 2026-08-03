# Subtask 01_01: Database Models for Soft Deletion

## Tasks
1. Add `is_deleted = Column(Boolean, default=False, nullable=False)` and `deleted_at = Column(DateTime, nullable=True)` to `User` and `Hub` models in `common/models/database.py`.
2. Generate and apply Alembic migration script under `migrations/versions/` for soft deletion columns.
3. Update database query helpers and SQLAlchemy selectors across `gateway/api/` to filter out soft-deleted entities (`User.is_deleted == False`) by default.
4. Ensure soft-deletion of a user revokes all active `UserSession` records immediately.
