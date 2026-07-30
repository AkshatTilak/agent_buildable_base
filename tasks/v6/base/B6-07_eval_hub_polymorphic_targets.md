# B6-07: Eval Hub — Polymorphic Targets & Flow Tracing

> **Status:** `[x]`  
> **Owner:** `projects/evalops`  
> **Secondary:** `gateway/api`, `common/models`  
> **Complexity:** 🔴 High (6 subtasks)

---

## Objective

Convert EvalOps into the **Eval Hub**. An eval suite is no longer bound to a single agent: it targets
**any agent or any workflow** in a hub the eval hub is linked to. Runs dispatch to the correct runner
based on target type, and workflow runs capture per-node traces so assertions can be made on any
intermediate state — not just the final answer.

---

## Acceptance Criteria

- [ ] `eval_test_suites`, `eval_run_history` and `eval_flow_traces` carry a `NOT NULL hub_id`; suite names are unique per hub.
- [ ] `eval_test_suites.agent_id` is replaced by a polymorphic pair: `target_type` (`agent` | `workflow`), `target_hub_id`, `target_id`.
- [ ] A suite may only target a resource in a hub the eval hub is linked to; the target is re-validated at run time via `hub_resolver`.
- [ ] A run dispatcher selects the agent runner or the workflow runner from `target_type`; both feed the same RAGAS / DeepEval metric pipeline.
- [ ] Workflow-targeted runs execute through the Workflow Hub run API and persist a `workflow_run` plus linked `eval_flow_traces`.
- [ ] Test cases support node-level assertions (`node_id`, `assertion_type`, `expected`) that are evaluated against captured trace state.
- [ ] Trace replay returns the full ordered node timeline with input/output state and latency for any run.
- [ ] Dataset import/export (CSV/JSON) is hub-scoped and rejects cross-hub suite ids.
- [ ] All routes are nested under `/hubs/{hub_id}/eval/...` and guarded by `require_hub(hub_type="eval")`; flat `/api/evalops/*` suite and run routes are **removed**.
- [ ] The eval dashboard aggregates per hub and can be filtered by target type, target, framework and date range.
- [ ] Existing suites, runs, metric results and traces migrate into `eval/default` with `target_type="agent"` and `target_hub_id` set to `agent/default`.
- [ ] `tests/test_eval_hub.py` covers polymorphic targeting, link enforcement, workflow trace assertions, and hub isolation.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-07a | Eval Model Hub Scoping & Polymorphic Targets | [`S6-07a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-07a.md) |
| S6-07b | Target Resolution & Runner Dispatch | [`S6-07b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-07b.md) |
| S6-07c | Workflow Trace Capture & Node Assertions | [`S6-07c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-07c.md) |
| S6-07d | Hub-Scoped Suite & Dataset Management | [`S6-07d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-07d.md) |
| S6-07e | Eval Hub API Routes & Dashboard Aggregation | [`S6-07e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-07e.md) |
| S6-07f | Eval Migration, Legacy Removal & Tests | [`S6-07f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-07f.md) |

---

## References

- [`references/logic/evalops.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/evalops.md)
- [`references/logic/workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) §2.3
- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.3, §5.4
