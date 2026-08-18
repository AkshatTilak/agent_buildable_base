---
version: 1.0.0
updated: 2026-08-18
id: design
links:
  - ../features/features.md
  - ../tasks/tasks.md
  - ../workflows/planning/planning.md
---

# Design — Master Guide & Index

> **Purpose**: All design is kept **separate** from tasks and references. Design
> answers *how the system should work*; tasks answer *what work must be done*.
> Keeping them separate prevents design decisions from being buried inside task
> files and makes them reviewable and versionable on their own.

## 1. The Design-First Rule

- **Workflows are designed BEFORE tasks are created.** A task is only created
  after the workflow/design it implements exists.
- Design is the source of truth for *intent*; tasks are the source of truth for
  *execution*.
- When a design changes, the affected tasks and features must be updated and
  cross-linked (see `../tasks/tasks.md` §2).

## 2. Directory Structure

```
design/
├── design.md          # this index
├── system/            # system architecture designs (topology, components, data flow)
├── workflows/         # workflow designs (designed BEFORE tasks)
└── ux/                # user-interaction / UX designs
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

## 3. Design File Format

Every design file uses the standard frontmatter (version, updated, id, links)
and includes:
- **Context / Problem**: what this design addresses.
- **Design**: the actual design (diagrams, decisions, rationale).
- **States**: empty/zero, populated, errored (per `../CONVENTIONS.md` §2).
- **Caveats / Trade-offs**: known limitations and why they were accepted.
- **Changelog**: record of design revisions.

## 4. Index

> Add new design files to this index as they are created.

| Design | Path | Status |
|--------|------|--------|
| _(none yet)_ | | |
