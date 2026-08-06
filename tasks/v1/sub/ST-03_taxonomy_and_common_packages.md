# Subtask: ST-03 — Taxonomy Service & Shared Core Packages

**Parent Link:** [BT-01: Foundation, Workspace & Core Services Base](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-01_foundation.md)

## Objective
Build shared monorepo packages (`packages/py-common`, `packages/api-contracts`, `packages/ts-common`) and implement `services/taxonomy-service/` for standardized care types, amenity definitions, pricing categories, and versioned assessment questionnaires.

---

## Technical Actionable Steps

- [x] **Step 1: Create `packages/py-common`**
  - Implement base Pydantic models, custom ORM mixins (timestamps, UUID primary keys), structured JSON logger, and DB connection pooling utilities.

- [x] **Step 2: Create `packages/api-contracts` & `packages/ts-common`**
  - Define OpenAPI schemas and script automated TypeScript code generation (`openapi-typescript`) into `packages/ts-common`.

- [x] **Step 3: Implement `services/taxonomy-service`**
  - Build endpoints for fetching unified taxonomies:
    - Care Types: Assisted Living, Memory Care, Independent Living, Skilled Nursing.
    - Amenities: Wheelchair Access, 24/7 Nursing, Pet Friendly, Memory Garden.
    - Payment Sources: Medicare, Medicaid, Private Pay, Veterans Aid.
    - Assessment Question Bank (versioned).

- [x] **Step 4: Seed Database with Initial Taxonomy Fixtures**
  - Create Alembic/Python seed scripts in `data/seeds/taxonomy_seed.py`.

---

## Dependencies
- Completion of `ST-01_monorepo_and_db_scaffold.md`.

## Complexity Rating
**Medium**

## Definition of Done
- `taxonomy-service` serves taxonomy endpoints with cached Redis responses.
- `packages/ts-common` exports clean TypeScript interfaces derived from OpenAPI contracts.
