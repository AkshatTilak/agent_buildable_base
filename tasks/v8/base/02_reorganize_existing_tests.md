# Base Task 2: Reorganize & Classify Existing Tests (v8)

## Objective
Migrate the 77 existing test files in `tests/` into the new layered directory structure without breaking any currently-passing tests, and consolidate submodule tests into the canonical root hierarchy.

## Why
The current flat `tests/` directory mixes unit, integration, and e2e concerns with no markers or structure. Submodule tests in `projects/*/tests/` are unit-level only and not part of a canonical run. This reorganization is a prerequisite for the layered testing infrastructure.

## Scope
- **Audit every existing test file** — classify each as `unit`, `integration`, or `e2e` based on what it actually tests vs. what it mocks.
- **Move files** into `tests/unit/` (majority), `tests/integration/gateway/` (a few lifecycle tests), preserving import paths.
- **Fix broken imports** — update any relative imports or fixture references broken by the move.
- **Migrate submodule tests** — consolidate `projects/*/tests/` files into the main `tests/` hierarchy under `tests/unit/syntraflow/`, `tests/unit/guardroute/`, `tests/unit/evalops/` (keeping submodule directories for project-specific dev, but having the canonical run from root).
- **Validate all migrated tests still pass** — `pytest tests/unit/ -v` must reproduce the same pass/fail as today.
- **Tag every test file** with the appropriate marker (`@pytest.mark.unit`, etc.).

## Associated Subtasks
1. `[ ]` `sub_02_01_audit_and_classify.md`: Audit all 77 test files and classify each as unit/integration/e2e.
2. `[ ]` `sub_02_02_migrate_and_fix_imports.md`: Move files into the new hierarchy, fix broken imports, and consolidate submodule tests.
3. `[ ]` `sub_02_03_validate_and_tag.md`: Validate all migrated tests still pass and tag every file with the correct marker.

## Definition of Done
- All 77 existing tests pass after migration (`pytest tests/unit/ -v` reproduces today's pass/fail).
- Submodule tests are consolidated under `tests/unit/{syntraflow,guardroute,evalops}/`.
- Every test file carries the correct `@pytest.mark.*` marker.
- No broken imports or fixture references remain.
