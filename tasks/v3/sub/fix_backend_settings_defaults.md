# Subtask: Fix Backend Settings Defaults

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [x] `common/config/settings.py:86` — Change `VRAM_BUDGET_MB` default from `20000` to `8000` (realistic for 16GB system).
- [x] Add `PLATFORM_VERSION: str = "3.0.0"` to `AppSettings` class.
- [x] Add `MAX_RAM_MB: int = 16384` to `AppSettings` class for RAM-aware resource allocation.
- [x] Update `.env` — add `PLATFORM_VERSION=3.0.0`, `VRAM_BUDGET_MB=8000`, `MAX_RAM_MB=16384`.
- [x] Update `.env.example` — mirror new vars with documentation comments.
- [x] Update `gateway/api/health.py` — include `platform_version` in health response.
- [x] Update `gateway/main.py` — set app version to `settings.PLATFORM_VERSION`.

## Dependencies
- None.

## Definition of Done
- `VRAM_BUDGET_MB` defaults to 8000.
- Health endpoint reports platform version.
- `.env` and `.env.example` include all new vars.
