# Subtask 04_01: Hubs Real Integration Tests

## Parent Base Task
`tasks/v8/base/04_hub_management_linking_real.md`

## Objective
Create `tests/integration/gateway/test_hubs_real.py` — hub lifecycle & cascading cleanup.

## Tasks
1. [ ] Create hub (agent / workflow / ingestion / eval types) → verify DB row + auto-membership as owner.
2. [ ] Update hub metadata (name, slug, settings).
3. [ ] Archive/restore hub → verify gated access.
4. [ ] Delete hub → verify cascading cleanup (members, links, bindings, agents, workflows).
5. [ ] Hub slug uniqueness enforcement.

## Definition of Done
- Hub CRUD, archive/restore, delete cascades, and slug uniqueness verified against real Postgres.
