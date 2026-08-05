# Subtask 06_01: Reference Documentation Audit & System Memory Update

## Tasks
1. [x] Audit and update reference files in `agent_buildable_base/references/`:
   - [x] `references/references.md`: Removed duplicate `auth.md` entry; marked user management as V7.
   - [x] `references/logic/auth.md`: Documented logout revokes all sessions; added `is_deleted`/`deleted_at` columns; clarified `.env` super admin bootstrapping.
   - [x] `references/logic/user_management.md`: Updated to V7; added soft-deleted state, hard-purge transition, and updated admin API table.
   - [x] `references/structure/system_architecture.md`: Added Gateway fail-fast error policy, structured exception hierarchy, bootstrapped accounts section, and host path persistence.
   - [x] `references/deployment/infrastructure.md`: Updated core services volume table to host bind mounts; added host path persistence and reset procedure.
2. [x] Verify all references maintain consistent terminology across the project.
3. [x] Updated `.env.example` with `SUPER_ADMIN_*`, `TEST_USER_*`, and `ALLOW_MEMBER_HUB_CREATION`.
4. [x] Added `ADMIN_EMAIL`, `ADMIN_PASSWORD`, `TEST_USER_EMAIL`, `TEST_USER_PASSWORD` to `common/config/settings.py`.
