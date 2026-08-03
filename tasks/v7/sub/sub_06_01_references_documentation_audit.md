# Subtask 06_01: Reference Documentation Audit & System Memory Update

## Tasks
1. Audit and update reference files in `agent_buildable_base/references/`:
   - `references/references.md`: Index of active v7 architecture documents.
   - `references/logic/auth.md`: Soft-deletion, session revocation, logout, and `.env` super admin bootstrapping.
   - `references/logic/user_management.md`: User lifecycle state transitions (active -> suspended -> soft-deleted -> hard-purged).
   - `references/structure/system_architecture.md`: Gateway fail-fast error policy and host path volume mounts.
   - `references/deployment/infrastructure.md`: Docker host path persistence instructions.
2. Verify all references maintain consistent terminology across the project.
