---
version: 1.0.0
updated: 2026-08-18
id: features
links:
  - ../design/design.md
  - ../tasks/tasks.md
  - ../references/references.md
---

# Features — Master Guide & Index

> **Purpose**: A **feature** is a user-visible capability of the system. Each
> feature is documented in its own folder under `features/`, with a spec that
> includes a mermaid diagram, full connections and paths, caveats, and a
> per-feature changelog.

## 1. What Belongs Here

- Feature **specifications** (what the feature does, how it connects).
- Feature **diagrams** (mermaid) showing full connections and paths.
- Feature **caveats** and known limitations.
- Feature **changelog** (every change to the feature).

Design rationale lives in `../design/`; execution work lives in `../tasks/`.
Features link to both.

## 2. Directory Structure

```
features/
├── features.md          # this index
└── _template/           # copy this folder to create a new feature
    ├── spec.md          # the feature spec (mermaid, connections, caveats)
    └── CHANGELOG.md     # per-feature changelog
```

## 3. Creating a Feature

1. Copy `features/_template/` to `features/<feature_name>/` (snake_case).
2. Fill in `spec.md`:
   - **Overview**: what the feature does.
   - **Mermaid diagram**: full connections and paths the feature considers.
   - **Connections**: how it links to other features, subsystems, data stores.
   - **States**: empty/zero, populated, errored (per `../CONVENTIONS.md` §2).
   - **Caveats**: known limitations, edge cases, and trade-offs.
   - **Backward compatibility**: whether changes preserve it (a user choice).
3. Link the feature from the relevant tasks (`../tasks/tasks.md` §2 `links`).
4. Add the feature to the index below.
5. Record changes in `features/<feature_name>/CHANGELOG.md`.

## 4. Feature Changelog

Every change to a feature (including updates that supersede an older task's
handling) is recorded in the feature's `CHANGELOG.md`, with links back to the
tasks that changed it.

## 5. Index

> Add new features to this index as they are created.

| Feature | Path | Status |
|---------|------|--------|
| _(none yet)_ | | |
