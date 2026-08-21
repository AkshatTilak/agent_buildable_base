---
version: 1.1.0
updated: 2026-08-20
id: design
links:
  - ../features/features.md
  - ../tasks/tasks.md
  - ../tasks/goal/goal.md
  - ../workflows/planning/planning.md
---

# Design (DDS) — Master Guide & Index

> **Purpose**: This directory, together with `../features/`, forms the **Design
> Document Specification (DDS)** — the single source of truth for **HOW** the
> system works. The SRS (`../tasks/goal/goal.md`) defines **WHAT/WHY**; the DDS
> answers HOW. Keeping design separate from tasks prevents design decisions
> from being buried inside task files and makes them reviewable and versionable
> on their own.

## 1. The Design-First Rule

- **Workflows are designed BEFORE tasks are created.** A task is only created
  after the workflow/design it implements exists.
- Design is the source of truth for *intent*; tasks are the source of truth for
  *execution*.
- When a design changes, the affected tasks and features must be updated and
  cross-linked (see `../tasks/tasks.md` §2).
- When the SRS changes, the affected designs must be updated first, then tasks
  are restructured (see `../workflows/planning/extend_goal.md`).

## 2. System Design: HLD & LLD

The DDS has exactly two levels. **Do not duplicate content between them or
into `../references/`** — link instead of copying.

### High-Level Design (HLD)
Defines system architecture, technology stack, database design, and major modules.

| Concern | Canonical location |
|---------|--------------------|
| Architecture, topology, major modules, data flow | `design/system/` (one narrative doc per subsystem) |
| Technology stack | `../STACK.md` (**single source** — never restated in design docs) |
| Database design | `../references/db/` (schema detail), summarized + linked from `design/system/` |
| Factual maps (routing, dependency graphs) | `../references/structure/` |

### Low-Level Design (LLD)
Specifies component logic, APIs, data structures, and workflows.

| Concern | Canonical location |
|---------|--------------------|
| Component logic, APIs, data structures, states | `../features/<feature>/spec.md` (per-component LLD spec) |
| End-to-end workflow logic (actors, steps, failure paths) | `design/workflows/` |
| User-interaction / UX flows | `design/ux/` |
| Business rules & decision matrices | `../references/logic/` |

**Output of both levels:** the DDS itself — the linked set of `design/` and
`features/` documents indexed here and in `../features/features.md`.

## 3. Directory Structure

```
design/
├── design.md          # this index (the DDS umbrella)
├── system/            # HLD: system architecture (topology, components, data flow)
├── workflows/         # LLD: workflow designs (designed BEFORE tasks)
└── ux/                # LLD: user-interaction / UX designs
```

### `system/`
High-level architecture: components, boundaries, data flow, integration points.
One file per subsystem or concern (snake_case).

### `workflows/`
End-to-end workflow designs. Each workflow describes the sequence of steps,
actors, decision points, and failure paths. **These are created before tasks.**

### `ux/`
User-interaction and UX designs: screens, flows, states (including zero/empty,
populated, and errored states per `../CONVENTIONS.md` §2).

## 4. Design File Format

Every design file uses the standard frontmatter (version, updated, id, links)
and includes:
- **Context / Problem**: what this design addresses (link the SRS requirement
  IDs it satisfies, e.g. `FR-001`).
- **Design**: the actual design (diagrams, decisions, rationale).
- **States**: empty/zero, populated, errored (per `../CONVENTIONS.md` §2).
- **Caveats / Trade-offs**: known limitations and why they were accepted.
- **Changelog**: record of design revisions.

## 5. Index

> Add new design files to this index as they are created.

| Design | Path | Level | Status |
|--------|------|-------|--------|
| _(none yet)_ | | | |

## 6. Changelog

- `1.1.0` (2026-08-20): Reframed as the DDS umbrella with explicit HLD/LLD
  split and anti-duplication mapping to `STACK.md`, `features/`, and
  `references/`.
- `1.0.0` (2026-08-18): Initial design guide.
