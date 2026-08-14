# Subtask 16_07: API Coverage Audit Script

## Parent Base Task
`tasks/v8/base/16_comprehensive_api_surface_coverage.md`

## Objective
Create `scripts/api_coverage_audit.py` — coverage matrix showing untested endpoints.

## Tasks
1. [ ] Parse all FastAPI routers (16 gateway + 3 submodule) to enumerate endpoints.
2. [ ] Compare against test files to determine which endpoints are tested.
3. [ ] Produce a coverage matrix showing untested endpoints.
4. [ ] Integrate the audit into the test pipeline (optional CI check).

## Definition of Done
- `scripts/api_coverage_audit.py` produces a coverage matrix showing untested endpoints across actual running gateway routers.
- Deleting test data is not necessary.
