# Subtask: Deployment Reference Update

**Parent Link:** `base/08_reference_documentation_sync.md`

## Actionable Steps
- [ ] Update `agent_buildable_base/references/deployment/infrastructure.md`:
  - Document Docker memory limit guidelines for 16GB RAM host systems.
  - Document Docker Compose profile usage (`core`, `full`, `admin`, `observability`).
  - Add native development troubleshooting guide when running core DBs in Docker + app servers natively.

## Dependencies
- `sub/docker_memory_limits.md`, `sub/docker_dev_profiles.md`.

## Definition of Done
- `references/deployment/infrastructure.md` includes V3 profile startup guides and RAM tuning docs.
