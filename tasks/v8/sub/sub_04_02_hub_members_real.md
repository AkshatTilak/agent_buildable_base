# Subtask 04_02: Hub Members Real Integration Tests

## Parent Base Task
`tasks/v8/base/04_hub_management_linking_real.md`

## Objective
Create `tests/integration/gateway/test_hub_members_real.py` — membership & role transitions.

## Tasks
1. [ ] Add member with role → verify `HubMember` row.
2. [ ] Role escalation/demotion (member → admin, admin → viewer).
3. [ ] Remove member → verify access revoked.
4. [ ] Owner transfer.
5. [ ] Member-created vs admin-created hub permission differences (`ALLOW_MEMBER_HUB_CREATION`).

## Definition of Done
- Membership add/remove, role transitions, owner transfer, and creation-permission differences verified against real Postgres.
