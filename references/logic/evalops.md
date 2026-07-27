# EvalOps — QA Verification & Observability Architecture

> **Source:** V5 Platform Maturity (B5-01)  
> **Last Updated:** 2026-07-23 (S5-01a, S5-01b, S5-01c, S5-01d Complete)

EvalOps serves as the automated quality assurance system, running benchmarks, managing evaluation test datasets, computing RAGAS and DeepEval metrics, analyzing transaction logs, and enforcing safety guardrails.

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
- **Test Suite CRUD:** Create, list, retrieve, update, delete, and clone evaluation test suites (`EvalTestSuite`).
- **Test Case CRUD:** Add, list, edit, and delete granular test cases (`EvalTestCase`).
- **Bulk Import/Export:**
  - CSV Format: Columns `input_query,expected_output,expected_context`.
  - JSON Format: Bulk import/export of test suites and cases with nested contexts.

---

## 4. Asynchronous Kafka Consumers & Eval Runner (`projects/evalops/src/runner/consumer.py` ✅)
- **Agent Evaluation Runner:** Listens to `agent-eval-trigger` Kafka topic (or background task fallback) to execute framework evaluations against target agent definitions.
- **Granular Database Results (`EvalMetricResult`):** Writes per-test-case, per-metric rows into `eval_metric_results` table containing score, threshold, pass/fail status, framework, and reason.
- **Aggregated History (`EvalRunHistory`):** Stores run status, duration, total cases, passed/failed counts, and aggregate scores for faithfulness, relevance, recall, precision, hallucination, toxicity, and bias.

---

## 5. Multi-Agent Flow Evaluation Tracing (V5 New)
- **State Interception:** EvalOps hooks into LangGraph `GraphState` events to capture intermediate outputs of custom blocks (IfElse, Routing, Agent Nodes, MCP Tools).
- **`EvalTrace` Schema:** Defines step-by-step state captures, allowing developers to write assertions against specific segments of a multi-agent workflow, rather than just the final output.
- **Terminal Node Mocks:** Enables mock testing of `ActionNode` side-effects (e.g., verifying a webhook payload without actually sending it).

---

## 6. REST API Routes (`projects/evalops/api.py` ✅)
- `POST /api/evalops/suites`: Create test suite.
- `GET /api/evalops/suites`: List suites (optional `?agent_id=` filter).
- `GET /api/evalops/suites/{id}`: Get suite details and contained cases.
- `PUT /api/evalops/suites/{id}`: Update suite metadata.
- `DELETE /api/evalops/suites/{id}`: Delete suite.
- `POST /api/evalops/suites/{id}/clone`: Clone test suite.
- `POST /api/evalops/suites/{id}/cases`: Add test case.
- `GET /api/evalops/suites/{id}/cases`: List test cases in suite.
- `PUT /api/evalops/cases/{id}`: Update test case.
- `DELETE /api/evalops/cases/{id}`: Delete test case.
- `POST /api/evalops/suites/{id}/import`: Bulk CSV/JSON import.
- `GET /api/evalops/suites/{id}/export`: Export suite as JSON.
- `POST /api/evalops/run`: Trigger evaluation run (`framework: ragas|deepeval|both`, `metrics`, `thresholds`).
- `GET /api/evalops/runs/{agent_id}`: List run history for agent.
- `GET /api/evalops/runs/detail/{run_id}/metrics`: Query granular per-metric DB results.
