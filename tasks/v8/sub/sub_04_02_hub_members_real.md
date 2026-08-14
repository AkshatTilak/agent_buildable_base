# Subtask 04_02: Hub Members Real Integration Tests

## Parent Base Task
`tasks/v8/base/04_hub_management_linking_real.md`

## Objective
Create `tests/integration/gateway/test_hub_members_real.py` — membership & role transitions.

## Tasks
1. [x] Add member with role → verify `HubMember` row.
2. [x] Role escalation/demotion (member → admin, admin → viewer).
3. [x] Remove member → verify access revoked.
4. [x] Owner transfer.
5. [x] Member-created vs admin-created hub permission differences (`ALLOW_MEMBER_HUB_CREATION`).

## Definition of Done
- Membership add/remove, role transitions, owner transfer, and creation-permission differences verified against actual running Postgres (`:5432`).
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend membership code.
- Deleting test data is not necessary.
