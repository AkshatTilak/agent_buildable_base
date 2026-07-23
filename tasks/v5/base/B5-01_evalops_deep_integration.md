# B5-01: EvalOps Deep Integration

> **Status:** `[x]`  
> **Owner:** `projects/evalops`  
> **Secondary:** `common/models`, `frontend`  
> **Complexity:** 🔴 High (8 subtasks)

---

## Objective

Elevate EvalOps from a basic synthetic-test-and-run tool into a comprehensive evaluation platform with full **RAGAS** and **DeepEval** framework integration, custom dataset management (CRUD + import/export), and a rich metrics dashboard showing recall, faithfulness, precision, relevance, hallucination, toxicity, and bias scores with historical trend charting.

---

## Acceptance Criteria

- [x] RAGAS evaluation runner computes: context_recall, faithfulness, answer_relevance, context_precision
- [x] DeepEval evaluation runner computes: hallucination, toxicity, bias, answer_relevancy, faithfulness
- [x] Users can create, edit, delete test suites and individual test cases via API
- [x] Users can bulk import test cases from CSV/JSON and export suites
- [x] Eval runs accept framework selection (RAGAS / DeepEval / both) and metric subset
- [x] Per-metric granular results stored in `eval_metric_results` table
- [x] Dashboard API returns aggregated stats and time-series trends
- [x] Frontend EvalPanel has Suite Manager, Run Config modal, Metrics Dashboard, Trend Charts, and Per-Case Drill-Down

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-01a | RAGAS Evaluation Runner | [`S5-01a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01a.md) |
| S5-01b | DeepEval Evaluation Runner | [`S5-01b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01b.md) |
| S5-01c | Custom Dataset Manager | [`S5-01c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01c.md) |
| S5-01d | Enhanced Eval Run Orchestration | [`S5-01d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01d.md) |
| S5-01e | DB Schema Expansion | [`S5-01e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01e.md) |
| S5-01f | Dashboard Stats & Trends API | [`S5-01f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01f.md) |
| S5-01g | Frontend: Suite Manager Tab | [`S5-01g.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01g.md) |
| S5-01h | Frontend: Results Dashboard | [`S5-01h.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-01h.md) |
