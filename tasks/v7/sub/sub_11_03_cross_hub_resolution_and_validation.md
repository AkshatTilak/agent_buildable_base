# Subtask 11_03: Cross-Hub Resource Resolution & Topology Validation (Verify + Test)

## Objective
Verify the existing cross-hub resolver and validation endpoint are correctly wired, then add the missing integration tests. The core services already exist — this is a verification-and-test pass, not greenfield construction.

## Current State (verified)
- `common/services/hub_resolver.py` already implements `resolve_linked()` (not `resolve_linked_resource`), `resolve_linked_many()`, `assert_link()`, `list_linked_hub_ids()`. `RESOURCE_TYPE_MODELS` maps `collection`/`agent`/`workflow`/`eval_suite` to their ORM models, with hub-boundary isolation, non-transitivity, and dual-hub RBAC enforcement.
- `projects/guardroute/src/core/graph_parser.py` already implements `validate_workflow_graph()` (module-level) and `GraphParser.validate_references()` returning `ValidationIssue` objects for: `EMPTY_GRAPH`, `DANGLING_EDGE`, `CYCLE_DETECTED`, `NON_TERMINAL_LEAF`, `MISSING_REFERENCE`, `MALFORMED_REFERENCE`, `REFERENCE_TYPE_MISMATCH`, `REFERENCE_TARGET_MISSING`, `HUB_ARCHIVED`, `HUB_LINK_REQUIRED`, `HUB_LINK_REVOKED`, `CROSS_HUB_REFERENCE_MISMATCH`, `REFERENCE_INACTIVE`.
- `gateway/api/workflows.py` already exposes `POST /hubs/{hub_id}/workflows/{wf_id}/validate` (returns `ValidationResult`) and `POST /hubs/{hub_id}/workflows/{wf_id}/run`.
- **Missing:** integration tests `tests/test_workflow_graph_refs.py` and `tests/test_workflow_hub.py`.

## Tasks
1. `[x]` **Verify resolver integration** — confirm `graph_parser.validate_references()` calls `resolve_linked()` with `source_hub_id` and enforces hub-boundary isolation + RBAC. Fix any drift (e.g. stale function names).
2. `[x]` **Verify validate endpoint** — confirm `POST /{wf_id}/validate` accepts an optional inline `graph` body (for unsaved drafts) and falls back to the stored draft when omitted. Confirm it returns node-level `ValidationIssue` items with `node_id`/`node_type`/`code`/`level`/`message` for the frontend to render.
3. `[/]` **Add `tests/test_workflow_graph_refs.py`**:
   - Rejects a graph with a cycle (`CYCLE_DETECTED`).
   - Rejects a graph with a dangling edge (`DANGLING_EDGE`).
   - Rejects a graph whose leaf is a non-terminal node (`NON_TERMINAL_LEAF`).
   - Rejects a node with a missing required reference (`MISSING_REFERENCE`).
   - Rejects a reference to a missing/inactive resource (`REFERENCE_TARGET_MISSING` / `REFERENCE_INACTIVE`).
4. `[/]` **Add `tests/test_workflow_hub.py`**:
   - Validates a well-formed graph with valid same-hub references passes.
   - Rejects a cross-hub reference when the source hub is not linked to the target hub (`HUB_LINK_REQUIRED` / `HUB_LINK_REVOKED`).
   - Rejects a cross-hub reference when the actor lacks RBAC permission on the target hub.
   - Confirms `POST /{wf_id}/validate` returns `is_valid=false` with node-level issues for an invalid graph.
5. `[/]` **Run the full workflow test suite** (`tests/test_workflow_*.py`) and confirm no regressions.
