# Subtask: ST-14 — Shared Mobile Application Shell (React Native / Flutter)

**Parent Link:** [BT-04: Client Applications & Shared Mobile Shell](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-04_client_surfaces.md)

## Objective
Build the cross-platform mobile application shell (`apps/mobile/`) sharing UI design tokens, API consumers, and native location services across iOS and Android builds.

---

## Technical Actionable Steps

- [ ] **Step 1: Bootstrap Mobile Project Shell**
  - Initialize React Native / Flutter project structure under `apps/mobile/`.
  - Configure build variants for B2C Family search view and B2B Facility staff quick-inbox view.

- [ ] **Step 2: Implement Authentication & Security Shell**
  - Connect mobile app auth wrapper to `auth-service` with secure token storage (Keychain/Keystore).

- [ ] **Step 3: Integrate Native Location & Spatial Search**
  - Use native GPS location services (`geolocation`) to query `search-service` using PostGIS radius queries.

- [ ] **Step 4: Build Facility Card & Inquiry Flow Views**
  - Implement mobile facility cards, swipeable photo carousels, and quick call/tour booking actions.

---

## Dependencies
- Completion of `ST-02_auth_and_rbac_service.md` and `ST-07_search_hybrid_engine.md`.

## Complexity Rating
**Medium**

## Definition of Done
- `apps/mobile` builds and executes in mobile simulators.
- Mobile app queries local GPS coordinates and renders nearby facility results.
