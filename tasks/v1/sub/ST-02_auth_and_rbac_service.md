# Subtask: ST-02 — Auth & RBAC Service Implementation

**Parent Link:** [BT-01: Foundation, Workspace & Core Services Base](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-01_foundation.md)

## Objective
Implement `services/auth-service/` using FastAPI to manage user authentication, JWT token issuance/validation, OIDC integration, and strict Role-Based Access Control (RBAC) middleware across platform roles.

---

## Technical Actionable Steps

- [x] **Step 1: Define Role Taxonomy & Scopes**
  - Implement enum for 9 platform roles: `family_guest`, `family_user`, `facility_staff_clinical`, `facility_admissions`, `facility_owner_admin`, `platform_support`, `platform_data_admin`, `platform_compliance_admin`, `platform_super_admin`.

- [x] **Step 2: Build FastAPI Service Scaffolding**
  - Create `services/auth-service/` with `pyproject.toml`, FastAPI app runner, and standard security endpoints (`/auth/login`, `/auth/register`, `/auth/refresh`, `/auth/verify`).

- [x] **Step 3: Implement JWT issuing & Security Middleware**
  - Use RS256/HS256 signing for JWT tokens containing user ID, role, facility claims, and expiration.
  - Create reusable FastAPI security dependencies (`require_role([...])`, `require_facility_scope(...)`).

- [x] **Step 4: Implement Audit Logging Hook for Support Impersonation**
  - Ensure any impersonation token generated for `platform_support` writes an immutable record into `audit_logs`.

---

## Dependencies
- Completion of `ST-01_monorepo_and_db_scaffold.md`.
- Libraries: `fastapi`, `pydantic`, `pyjwt`, `passlib`.

## Complexity Rating
**Medium**

## Definition of Done
- Unit tests verify JWT issuance, role token parsing, and permission denial on insufficient roles.
- Support impersonation actions trigger an audit log entry.
