---
id: goal_001
version: 1.0.0
updated: YYYY-MM-DD
status: not_started
depends_on: []
links:
  - ../../design/design.md
  - ../../features/features.md
  - ../../references/references.md
---

# SRS: <System Name> — Software Requirements Specification

> This Goal file **is** the SRS (Requirement Specification). It is the single
> source of truth for **WHAT** the system must do and **WHY**. **HOW** it does
> it lives in the DDS (Design Document Specification) — see
> `../../design/design.md`.
>
> **Revision rule:** every change to this file bumps `version` and adds a
> Changelog entry, then triggers the task-restructuring procedure in
> `../../workflows/planning/extend_goal.md`.

## 1. Introduction
### 1.1 Purpose
One-sentence statement of the ultimate system objective.
### 1.2 Scope
What the system covers — and what it explicitly does NOT do (non-goals).
### 1.3 Definitions & Acronyms
Domain terms used by the requirements below.
### 1.4 References
- DDS (System Design): `../../design/design.md`
- Feature registry (LLD specs): `../../features/features.md`
- Conventions & philosophy: `../../CONVENTIONS.md`, `../../CODING_PHILOSOPHY.md`

## 2. Overall Description
### 2.1 Product Perspective
Where this system sits; upstream/downstream context.
### 2.2 User Characteristics
Who or what consumes the system.
### 2.3 Constraints
Technical, regulatory, and process constraints.
### 2.4 Assumptions & Dependencies
External systems, services, and assumptions the requirements rely on.

## 3. Functional Requirements
Requirement IDs are **stable and never reused**. A retired requirement is struck
through and noted in the Changelog, not deleted. Each active requirement maps to
at least one Base Task via that task's `srs_refs` frontmatter.
- [ ] **FR-001**: <testable requirement statement>
- [ ] **FR-002**: <testable requirement statement>

## 4. Non-Functional Requirements
- [ ] **NFR-001**: <performance / security / observability requirement>
- [ ] **NFR-002**: <...>

## 5. External Interface Requirements
- [ ] **IR-001**: <API / UI / CLI / hardware interface requirement>

## 6. Success Criteria
- [ ] Measurable definition of "done" for the whole system.
- [ ] All Base Tasks in the registry below are `[x]`.

## 7. Base Task Registry
Each Base Task satisfies one or more requirements above (recorded in its
`srs_refs` frontmatter). Keep this checklist in sync when tasks are restructured.
- [ ] `base/<task_name>.md` — satisfies FR-001, FR-002
- [ ] `base/<task_name>.md` — satisfies NFR-001

## 8. User Decisions
Recorded user choices that shaped this SRS (see
`../../workflows/user/user_input.md`).

## 9. Changelog
- `1.0.0` (YYYY-MM-DD): Initial SRS definition.
