# Subtask 02_03: Validate Migrated Tests & Tag Markers

## Parent Base Task
`tasks/v8/base/02_reorganize_existing_tests.md`

## Objective
Validate all migrated tests still pass and tag every file with the correct marker.

## Tasks
1. [ ] Run `pytest tests/unit/ -v` and confirm it reproduces the same pass/fail as today.
2. [ ] Tag every test file with the appropriate marker (`@pytest.mark.unit`, `@pytest.mark.integration`, etc.).
3. [ ] Fix any tests that break during migration (imports, fixtures, config).
4. [ ] Confirm `--strict-markers` passes with all registered markers.

## Definition of Done
- All 77 existing tests pass after migration (`pytest tests/unit/ -v` reproduces today's pass/fail).
- Every test file carries the correct `@pytest.mark.*` marker.
- `--strict-markers` passes.
