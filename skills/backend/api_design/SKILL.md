---
version: 2.0.0
updated: 2026-08-18
id: skill_backend_api_design
links:
  - ../../qa/backend/SKILL.md
  - ../../../references/db/db.md
  - ../../../references/structure/
  - ../../../references/logic/
  - ../../../workflows/execution/work_verification.md
  - ../../../CONVENTIONS.md
---

# Skill: API Design

> **Purpose**: Design consistent, evolvable, and well-typed REST and GraphQL
> APIs. Enforces schema-first design, clear error contracts, versioning
> discipline, and backward-compatibility awareness. Adapted from
> wshobson/agents `api-design-principles` and addyosmani/agent-skills
> `api-and-interface-design`.

## Trigger Conditions
- Load this skill when designing or modifying API endpoints, routes, or GraphQL
  schemas.
- Load when `../../qa/backend/SKILL.md` identifies API design issues.
- Load when the user asks to create, extend, or review an API.

## Steps

### 1. Schema-First Design
- Define the API contract **before** writing implementation code.
- For REST: define request/response shapes, status codes, and error bodies.
- For GraphQL: define the schema (types, queries, mutations, subscriptions)
  first.
- Document the schema in `../../../references/structure/`.

### 2. Resource Modeling
- Model resources as nouns, not verbs (e.g. `/users`, not `/getUsers`).
- Use HTTP methods semantically: GET (read), POST (create), PUT/PATCH (update),
  DELETE (remove).
- Nest resources only when the relationship is truly compositional (e.g.
  `/users/:id/orders`). Avoid deep nesting (>2 levels).
- Keep request/response shapes flat and focused. Avoid generic wrappers that
  obscure the actual data.

### 3. Error Contracts
- Define a consistent error response shape across all endpoints.
- Use appropriate HTTP status codes: 400 (bad request), 401 (unauthorized), 403
  (forbidden), 404 (not found), 409 (conflict), 422 (unprocessable), 500
  (internal error).
- Error bodies must include: a machine-readable error code, a human-readable
  message, and (in development) relevant context.
- Follow `../../../workflows/quality/fallback_policy.md`: no blanket 500
  responses that swallow real errors.

### 4. Versioning & Backward Compatibility
- Version the API from the start (URL path `/v1/`, header `Accept-Version`, or
  query param).
- **Backward compatibility is a choice**: ask the user whether to preserve or
  break it (per `../../../workflows/planning/planning.md` §4).
- When breaking: bump the major version, document the migration path, and
  support both versions during a deprecation window.
- When preserving: add fields, never remove or rename them; use deprecation
  notices.

### 5. Input Validation & Type Safety
- Validate all inputs at the API boundary before they reach business logic.
- Use strict types: no `any`, no untyped dictionaries, no stringly-typed
  values.
- Reject unknown fields (fail closed, not open).
- Sanitize outputs: never leak stack traces, internal paths, or database errors
  to clients.

### 6. Performance & Pagination
- Paginate all list endpoints (cursor-based preferred for large datasets;
  offset-based acceptable for small/stable datasets).
- Support field selection (sparse fieldsets) and expansion (embed related
  resources) to avoid over-fetching.
- Set rate limits and document them.

### 7. Verification
- Verify the API contract with integration tests (Track 2, per
  `../../../references/tests/tests.md`).
- Verify error responses for all defined error cases.
- Verify backward compatibility if applicable.
- Run `../../qa/backend/SKILL.md` checks on the implementation.

## Expected Outputs
- API schema documented in `../../../references/structure/`.
- Consistent error contract across all endpoints.
- Versioning strategy documented.
- Integration tests covering all endpoints and error cases.

## References
- `../../qa/backend/SKILL.md` — backend QA checks.
- `../../../references/db/db.md` — database conventions.
- `../../../references/structure/` — architecture and topology.
- `../../../references/logic/` — business logic.
- `../../../workflows/execution/work_verification.md` — verification protocol.
- `../../../CONVENTIONS.md` — naming conventions.
- Adapted from: wshobson/agents `api-design-principles` and
  addyosmani/agent-skills `api-and-interface-design`.
