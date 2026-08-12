# Subtask 11_01: Database Schema & ORM Model Verification + Run Step Telemetry

## Objective
Verify the existing workflow ORM models and Pydantic schemas already satisfy the visual graph contract, then add the one missing piece — per-node execution step telemetry (`WorkflowRunStep`) — with its Alembic migration and hub-scoped query guards.

## Current State (verified)
- `WorkflowDefinition` (`common/models/database.py`, ~line 424): already has `hub_id`, `name`, `slug`, `description`, `tags_json`, `status`, `published_version_id`, `draft_version_id`, `created_by`, `created_at`, `updated_at`, plus `UniqueConstraint("hub_id", "slug")`.
- `WorkflowVersion` (~line 454): already has `workflow_id`, `version_number`, `graph_json` (JSON), `change_note`, `is_valid`, `validation_json`, `created_by`, `created_at`, `UniqueConstraint("workflow_id", "version_number")`. Optimistic concurrency is handled by `version_service.compute_etag()` — **do not** add redundant `etag`/`commit_message`/`author_user_id` columns.
- `WorkflowRun` (~line 475): already has `hub_id`, `workflow_id`, `version_id`, `trigger`, `input_json`, `output_json`, `status`, `error_message`, `node_count`, `duration_ms`, `started_by`, `started_at`, `finished_at`, `Index("ix_workflow_runs_wf_started", ...)`.
- `EvalFlowTrace` (~line 500): already captures per-node trace events (`node_id`, `node_type`, `sequence`, `input_state`, `output_state`, `latency_ms`, `timestamp`).
- Schemas in `common/schemas/workflows.py` already cover `NodeReference`, `WorkflowGraph`, `ValidationIssue`, `ValidationResult`, and all workflow/version/run DTOs.
- Migrations live in **`migrations/versions/`** (not `common/migrations/versions/`).

## Tasks
1. `[x]` **Verify existing models** — Confirm `WorkflowDefinition`, `WorkflowVersion`, `WorkflowRun` fields and indexes match the above. Do not duplicate existing columns.
2. `[x]` **Add `WorkflowRunStep` model** in `common/models/database.py`:
   - `id` (UUID PK), `run_id` (FK → `workflow_runs.id`, CASCADE, indexed), `hub_id` (HubScopedMixin), `workflow_id` (FK, indexed).
   - `node_id` (String), `node_type` (String), `sequence` (Integer).
   - `input_state` (JSON), `output_state` (JSON), `error_json` (JSON, nullable).
   - `status` (String: `pending | running | succeeded | failed | skipped`), `latency_ms` (Float), `started_at`, `finished_at`.
   - `__table_args__` with `Index("ix_workflow_run_steps_run_seq", "run_id", "sequence")`.
   - Register in `common/models/__init__.py` exports.
3. `[x]` **Alembic migration** in `migrations/versions/` (e.g. `m4n5o6p7q8r9_v7_workflow_run_steps.py`) adding the `workflow_run_steps` table, FKs, and indexes. Follow the naming/pattern of existing V7 migrations.
4. `[x]` **Pydantic schemas** in `common/schemas/workflows.py`:
   - `WorkflowRunStepSummary` (id, run_id, node_id, node_type, sequence, status, latency_ms, started_at, finished_at).
   - `WorkflowRunStepDetail` (adds `input_state`, `output_state`, `error_json`).
   - Extend `WorkflowRunDetail` with `steps: List[WorkflowRunStepSummary] = []`.
5. `[x]` **Hub-scoped query guard** — ensure any new query on `WorkflowRunStep` filters by `hub_id` (hubs.md §5.3). Add a test asserting the guard in `tests/test_hub_query_guard.py` style if not already covered.
