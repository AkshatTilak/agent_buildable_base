# Base Task 4: Real-World Integration Tests — Hub Management & Linking (v8)

## Objective
Test hub CRUD, membership management, hub linking, datastore bindings, and cross-hub access against real Postgres.

## Why
Hub management is the core multi-tenant primitive. Existing tests are mock-only and never verify cascading cleanup, membership role transitions, link revocation, or datastore binding encryption against a real database.

## Scope
- **`tests/integration/gateway/test_hubs_real.py`** — Hub lifecycle:
  - Create hub (agent / workflow / ingestion / eval types) → verify DB row + auto-membership as owner.
  - Update hub metadata (name, slug, settings).
  - Archive/restore hub → verify gated access.
  - Delete hub → verify cascading cleanup (members, links, bindings, agents, workflows).
  - Hub slug uniqueness enforcement.
- **`tests/integration/gateway/test_hub_members_real.py`** — Membership:
  - Add member with role → verify `HubMember` row.
  - Role escalation/demotion (member → admin, admin → viewer).
  - Remove member → verify access revoked.
  - Owner transfer.
  - Member-created vs admin-created hub permission differences (`ALLOW_MEMBER_HUB_CREATION`).
- **`tests/integration/gateway/test_hub_links_real.py`** — Hub linking:
  - Create link between two hubs → verify bidirectional access.
  - Cross-hub data access (agent hub accessing ingestion hub's collections).
  - Link revocation → verify access denied.
  - Link visibility (which resources are exposed through links).
- **`tests/integration/gateway/test_datastore_bindings_real.py`** — Datastore binding:
  - Bind Qdrant collection to hub → verify `DatastoreBinding` row.
  - Bind external DB connector → test credential encryption/decryption.
  - Unbind → verify cleanup.

## Associated Subtasks
1. `[x]` `sub_04_01_hubs_real.md`: `test_hubs_real.py` — hub lifecycle & cascading cleanup.
2. `[x]` `sub_04_02_hub_members_real.md`: `test_hub_members_real.py` — membership & role transitions.
3. `[x]` `sub_04_03_hub_links_real.md`: `test_hub_links_real.py` — hub linking & cross-hub access.
4. `[x]` `sub_04_04_datastore_bindings_real.md`: `test_datastore_bindings_real.py` — datastore binding & credential encryption.

## Definition of Done
- Hub CRUD, archive/restore, delete cascades, and slug uniqueness verified against real Postgres.
- Membership add/remove, role transitions, owner transfer, and creation-permission differences verified.
- Hub linking, cross-hub access, link revocation, and link visibility verified.
- Datastore binding, credential encryption/decryption, and unbind cleanup verified.
