# Subtask 02_02: Migrate Tests & Fix Broken Imports

## Parent Base Task
`tasks/v8/base/02_reorganize_existing_tests.md`

## Objective
Move test files into the new hierarchy, fix broken imports, and consolidate submodule tests.

## Tasks
1. [x] Move files into `tests/unit/` (majority) and `tests/integration/gateway/` (a few lifecycle tests) per the classification matrix.
2. [x] Fix broken imports — update any relative imports or fixture references broken by the move.
3. [x] Consolidate `projects/*/tests/` files into `tests/unit/syntraflow/`, `tests/unit/guardroute/`, `tests/unit/evalops/` (keeping submodule directories for project-specific dev, but having the canonical run from root).
4. [x] Ensure shared fixtures/helpers referenced by moved tests are accessible from the new locations.

## Definition of Done
- All test files moved to their target directories.
- No broken imports or fixture references remain.
- Submodule tests consolidated under `tests/unit/{syntraflow,guardroute,evalops}/`.
