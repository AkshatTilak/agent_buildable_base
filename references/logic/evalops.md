# EvalOps — QA Verification & Observability Architecture

> **V6 Update — Hub Scoping.** EvalOps is now the **Eval Hub**: suites live inside an eval hub and may target **either** an agent **or** a whole workflow in any hub the eval hub is linked to, instead of a single global agent id.
> **V7 Update — Judge Sync & Error Boundaries.** EvalOps judge models dynamically sync with GuardRoute model registry (with local judge fallback when remote API keys are missing); metric calculations are isolated in explicit exception boundaries with trace logging to `evalops.log`.
> All resources described below are scoped by `hub_id`. See
> [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md)
> for the canonical tenancy model.

> **Source:** V5 Platform Maturity (B5-01)  
> **Last Updated:** 2026-08-05 (V7 platform stabilization)

EvalOps serves as the automated quality assurance system, running benchmarks, managing evaluation test datasets, computing RAGAS and DeepEval metrics, analyzing transaction logs, and enforcing safety guardrails.

---

## 0. Hub-Scoped Storage & Polymorphic Targets (V6)

| Table | Change |
|---|---|
| `eval_test_suites` | **`NOT NULL hub_id`** FK `hubs.id` (`hub_type = 'eval'`), indexed; suite names are unique per hub — `UNIQUE (hub_id, name)` |
| `eval_test_cases` | inherits its hub via `suite_id` |
| `eval_run_history` | **`NOT NULL hub_id`**, indexed |
| `eval_metric_results` | inherits its hub via `run_id` |
| `eval_flow_traces` | **`NOT NULL hub_id`**, indexed; `run_id` → `workflow_runs.id` for workflow-targeted runs |

### Polymorphic evaluation target

`eval_test_suites.agent_id` is **removed** and replaced by a polymorphic target triple:

```text
target_type     String(20)  not null    # agent | workflow
target_hub_id   String(36)  not null    # the hub that owns the target
target_id       String(36)  not null    # agent_definitions.id | workflows.id
```

* A suite may only target a resource in a hub the eval hub is **linked to** (`eval → agent` or
  `eval → workflow`; see [`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.3).
  Validation runs through `common/services/hub_resolver.py` when the suite is saved.
* The target is **re-validated at run time**. If the link was revoked or the target deleted, the run
  fails fast with `HUB_LINK_REVOKED` / `TARGET_NOT_FOUND` instead of evaluating stale data.
* Nothing may link *into* an eval hub — eval is always the consumer.

---

## 1. RAGAS Retrieval Quality Benchmarking (`projects/evalops/src/metrics/ragas_runner.py` ✅)
- **Context Recall & Precision:** Measures overlap of retrieved document segments with expected context.
- **Faithfulness Metric Check:** Verifies agent responses remain grounded in retrieved contexts without hallucinations.
- **Answer Relevancy:** Evaluates alignment between generated response and user input query.
- **Judge Model & Embeddings:** Uses platform's configured LiteLLM models and vector client embeddings.

---

## 2. DeepEval Safety & Guardrails Engine (`projects/evalops/src/metrics/deepeval_runner.py` ✅)
- **Judge Model:** Configured via platform's LiteLLM completion model (`gemini/gemini-3.5-flash`).
- **Metrics Evaluated:**
  - `HallucinationMetric`: Asserts actual output is factual with respect to context.
  - `ToxicityMetric`: Asserts output is free from hate speech, harassment, and toxic language.
  - `BiasMetric`: Detects demographic or gender bias in responses.
  - `AnswerRelevancyMetric` & `FaithfulnessMetric`.
- **Per-Metric Thresholds & Pass/Fail Assertions:** Configurable thresholds per run request with reasons.

---

## 3. Dataset Manager & Bulk Import/Export (`projects/evalops/src/datasets/manager.py` ✅)
- **Test Suite CRUD:** Create, list, retrieve, update, delete, and clone evaluation test suites (`EvalTestSuite`). All operations are scoped by `hub_id`; a bare suite id never resolves.
- **Test Case CRUD:** Add, list, edit, and delete granular test cases (`EvalTestCase`).
- **Node-level assertions (V6):** a test case may carry assertions against intermediate workflow state in addition to the final output:

  | Field | Meaning |
  |---|---|
  | `node_id` | the workflow node whose captured state is asserted on |
  | `assertion_type` | `contains` \| `equals` \| `regex` \| `json_path` \| `not_contains` \| `latency_under` |
  | `expected` | expected value / pattern / threshold |

  Assertions are evaluated against the state captured in `eval_flow_traces` for the matching node, and each result is written as a row in `eval_metric_results` alongside the RAGAS/DeepEval metric rows.
- **Bulk Import/Export:**
  - CSV Format: Columns `input_query,expected_output,expected_context`.
  - JSON Format: Bulk import/export of test suites and cases with nested contexts, including node-level assertions.

---

## 4. Asynchronous Kafka Consumers & Eval Runner (`projects/evalops/src/runner/consumer.py` ✅)
- **Run Dispatcher (V6):** on trigger, the dispatcher reads `target_type` from the suite and selects the runner — the **agent runner** or the **workflow runner**. Both feed the *same* RAGAS/DeepEval metric pipeline, so metric definitions, thresholds and result shapes are identical regardless of target.
- **Agent Evaluation Runner:** Listens to `agent-eval-trigger` Kafka topic (or background task fallback) to execute framework evaluations against the target agent definition, resolved as `(target_hub_id, target_id)`.
- **Workflow Evaluation Runner (V6):** executes the target workflow through the Workflow Hub run API (`POST /hubs/{target_hub_id}/workflows/{target_id}/run`, `trigger = eval`) and captures per-node `eval_flow_traces` linked by `run_id` → `workflow_runs.id`. The workflow's final output feeds the metric pipeline; intermediate node state feeds the node-level assertions from §3.
- **Granular Database Results (`EvalMetricResult`):** Writes per-test-case, per-metric rows into `eval_metric_results` table containing score, threshold, pass/fail status, framework, and reason. Node-level assertion outcomes land in the same table.
- **Aggregated History (`EvalRunHistory`):** Stores `hub_id`, target triple, run status, duration, total cases, passed/failed counts, and aggregate scores for faithfulness, relevance, recall, precision, hallucination, toxicity, and bias.

---

## 5. Multi-Agent Flow Evaluation Tracing (V5 New)
- **State Interception:** EvalOps hooks into LangGraph `GraphState` events to capture intermediate outputs of custom blocks (IfElse, Routing, Agent Nodes, MCP Tools).
- **`EvalTrace` Schema:** Defines step-by-step state captures, allowing developers to write assertions against specific segments of a multi-agent workflow, rather than just the final output.
- **Terminal Node Mocks:** Enables mock testing of `ActionNode` side-effects (e.g., verifying a webhook payload without actually sending it).
- **V6:** `eval_flow_traces` rows carry `hub_id` and are keyed by `run_id` → `workflow_runs.id`, so the Workflow Hub and the Eval Hub share one tracing mechanism rather than two. Traces are readable only from the eval hub that produced them.

---

## 6. REST API Routes (`projects/evalops/api.py` ✅)

All EvalOps routes are nested under an eval hub and guarded by `require_hub(hub_type='eval', ...)`.
The flat `/api/evalops/*` suite and run routes are **removed** — no back-compat aliases are retained.

- `POST /hubs/{hub_id}/eval/suites`: Create test suite (`target_type`, `target_hub_id`, `target_id`; `UNIQUE (hub_id, name)`).
- `GET /hubs/{hub_id}/eval/suites`: List suites (optional `?target_type=` / `?target_id=` filter; the V5 `?agent_id=` filter is gone).
- `GET /hubs/{hub_id}/eval/suites/{id}`: Get suite details and contained cases.
- `PUT /hubs/{hub_id}/eval/suites/{id}`: Update suite metadata or retarget it (target re-validated against hub links).
- `DELETE /hubs/{hub_id}/eval/suites/{id}`: Delete suite.
- `POST /hubs/{hub_id}/eval/suites/{id}/clone`: Clone test suite.
- `POST /hubs/{hub_id}/eval/suites/{id}/cases`: Add test case (including node-level assertions).
- `GET /hubs/{hub_id}/eval/suites/{id}/cases`: List test cases in suite.
- `PUT /hubs/{hub_id}/eval/cases/{id}`: Update test case.
- `DELETE /hubs/{hub_id}/eval/cases/{id}`: Delete test case.
- `POST /hubs/{hub_id}/eval/suites/{id}/import`: Bulk CSV/JSON import.
- `GET /hubs/{hub_id}/eval/suites/{id}/export`: Export suite as JSON.
- `POST /hubs/{hub_id}/eval/runs`: Trigger evaluation run (`framework: ragas|deepeval|both`, `metrics`, `thresholds`); the dispatcher picks the agent or workflow runner from the suite's `target_type`.
- `GET /hubs/{hub_id}/eval/runs`: List run history for the hub (optional `?suite_id=` / `?target_id=` filter).
- `GET /hubs/{hub_id}/eval/runs/{run_id}/metrics`: Query granular per-metric DB results.
- `GET /hubs/{hub_id}/eval/traces?run_id=`: Per-node flow traces for a workflow-targeted run.

---

## 7. V6 Route Surface

| Method | Path | Min role | Purpose |
|---|---|---|---|
| `GET` / `POST` | `/hubs/{hub_id}/eval/suites` | viewer / contributor | List, create suites (polymorphic target) |
| `GET` / `PUT` / `DELETE` | `/hubs/{hub_id}/eval/suites/{id}` | viewer / contributor / maintainer | Inspect, retarget, delete |
| `POST` | `/hubs/{hub_id}/eval/suites/{id}/clone` | contributor | Duplicate a suite |
| `GET` / `POST` | `/hubs/{hub_id}/eval/suites/{id}/cases` | viewer / contributor | Test cases + node-level assertions |
| `POST` / `GET` | `/hubs/{hub_id}/eval/suites/{id}/import`, `/export` | contributor / viewer | Bulk CSV/JSON |
| `POST` | `/hubs/{hub_id}/eval/runs` | contributor | Trigger a run (agent or workflow target) |
| `GET` | `/hubs/{hub_id}/eval/runs` | viewer | Hub-scoped run history |
| `GET` | `/hubs/{hub_id}/eval/runs/{run_id}/metrics` | viewer | Per-metric and per-assertion results |
| `GET` | `/hubs/{hub_id}/eval/traces` | viewer | Per-node `eval_flow_traces` for a run |
