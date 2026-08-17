# Subtask 08_03: Multi-User Collaboration E2E Test

## Parent Base Task
`tasks/v8/base/08_end_to_end_workflow_tests.md`

## Objective
Create `tests/e2e/flows/test_multi_user_collaboration.py` — multi-user collaboration.

## Tasks
1. [x] Admin creates hub → invites member.
2. [x] Member accepts invite → gains access.
3. [x] Both users create resources in the same hub → verify visibility.
4. [x] Admin demotes member to viewer → verify write access revoked.
5. [x] Admin removes member → verify access fully revoked.

## Definition of Done
- Multi-user collaboration journey passes end-to-end against actual running dev services.
- Invite acceptance, resource visibility, demotion, and removal verified.
- If errors arise, inspect Docker container logs (`docker compose logs db`) and fix root causes directly in backend collaboration code.
- Deleting test data is not necessary.
