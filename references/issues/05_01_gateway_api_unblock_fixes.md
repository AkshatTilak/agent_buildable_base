# Resolved Blockers: Gateway API Integration Test Suite (Subtask 05_01)

## Summary
During implementation of `tests/test_v7_gateway_api_suite.py`, three existing code defects blocked the integration tests. These were minimal, backward-compatible fixes applied directly to the affected modules rather than creating new dependencies or wrapper code.

## Fixes Applied

### 1. `User.is_active` property missing
- **File:** `common/models/database.py`
- **Symptom:** `POST /api/hubs/{hub_id}/members` raised `AttributeError: 'User' object has no attribute 'is_active'`.
- **Fix:** Added an `is_active` property returning `self.status == "active"`.
- **Verification:** Hub membership tests pass; no schema migration required.

### 2. `list_hub_links` repository call signature mismatch
- **File:** `gateway/api/hubs.py`
- **Symptom:** `GET /api/hubs/{hub_id}/links` raised `TypeError: list_links() got an unexpected keyword argument 'hub_id'`.
- **Fix:** Updated the endpoint to call `list_links(db, source_hub_id=ctx.hub_id)` and added inline handling for `direction=incoming`.
- **Verification:** Hub links CRUD test passes.

### 3. `archive_hub` repository call signature mismatch
- **File:** `gateway/api/hubs.py`
- **Symptom:** `POST /api/hubs/{hub_id}/archive` raised `TypeError: archive_hub() missing 1 required keyword-only argument: 'archived'`.
- **Fix:** Updated the endpoint to call `archive_hub(db, hub_id=ctx.hub_id, archived=True)`.
- **Verification:** Hub archive test passes.

## Verification Command
```powershell
poetry run pytest tests/test_v7_gateway_api_suite.py -xvs
```
Result: **12 passed**.

## Scope Rationale
These fixes were strictly the minimum changes required to unblock the integration test suite. They correct existing in-code inconsistencies (model property missing, call sites out of sync with repository signatures) rather than introducing new abstractions or dependencies.
