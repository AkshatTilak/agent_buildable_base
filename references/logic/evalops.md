# EvalOps — QA Verification & Observability Architecture

> **Source:** Migrated from `requirements/evalops.md`
> **Last Updated:** 2026-07-14

EvalOps serves as the automated quality assurance system, running benchmarks, analyzing transaction logs from Kafka, and enforcing safety guardrails. It runs as a **test/CI tool**, not a permanent production service.

---

## 1. RAGAS Retrieval Quality Benchmarking (Pending)
- **Context Recall Verification:** Measure overlap of retrieved document segments with ground truth.
- **Faithfulness Metric Check:** Verify Synthesis Agent's response remains grounded in context without hallucinations.
- **Semantic Similarity Scoring:** Compute embedding similarity scores comparing synthesized output to ground truths. Uses active embedding model from Model Registry (default: jina-clip-v2).

---

## 2. DeepEval Safety & Guardrails Assertions (Pending)
- **Judge Model:** Configure to use platform's primary LLM (`gemini/gemini-3.5-flash` via LiteLLM) instead of default `gpt-4o`. Add `DEEPEVAL_MODEL` setting.
- **Toxicity Metric Check:** Assert toxicity < 0.1 for typical user prompts.
- **Prompt Injection Scanner:** Test orchestrator against injection payloads from `tests/fixtures/injection_payloads.json`.
- **PII Leakage Detector:** Verify no PII output (phone numbers, API keys, emails).

---

## 3. Router & Logic Benchmarking Scripts (Pending)

### `bench_gguf.py`
- Evaluate active classifier model F1-score against test dataset at `tests/fixtures/grpo_eval_data.json`.
- Test dataset schema:
  ```json
  [
    {"prompt": "What is 2+2?", "expected_complexity": "simple", "expected_agents": []},
    {"prompt": "Search for recent AI papers and summarize", "expected_complexity": "complex", "expected_agents": ["retrieval", "web_search"]}
  ]
  ```
- Track cold-start vs warm latencies. Assert VRAM freed after idle timeout.

### `bench_mmlu.py`
- Run standardized logic benchmarks (GSM8k, HumanEval, MMLU subsets) against orchestrator's synthesis node.
- Measure primary provider failure injection → verify fallback chain works.

### `bench_models.py` (NEW)
- Run benchmarks across different model options from Model Registry.
- Compare accuracy, latency, VRAM usage per role (OCR, ASR, Embedding, Classifier).
- Output comparison table for model selection decisions.

---

## 4. Asynchronous Kafka Consumers & Eval Runner ✅
- **Real-Time Log Stream Processing:** Background consumers for `guardroute-traces`, `syntraflow-ingestion-jobs`, and `agent-eval-trigger` Kafka topics.
- **Agent Evaluation Runner (`projects/evalops/src/runner/consumer.py`):** Listens to `agent-eval-trigger` topic to asynchronously execute benchmark test cases against target models and update `EvalRunHistory` table with faithfulness, relevance, and latency metrics.
- **Automated Report Generation:** Write to `evalops_reports` PostgreSQL table:
  ```sql
  CREATE TABLE evalops_reports (
      id              SERIAL PRIMARY KEY,
      run_id          UUID NOT NULL,
      benchmark_name  VARCHAR(100) NOT NULL,
      metric_name     VARCHAR(100) NOT NULL,
      metric_value    FLOAT NOT NULL,
      model_name      VARCHAR(200),
      timestamp       TIMESTAMP DEFAULT NOW(),
      metadata_json   JSONB
  );
  ```
- Flag anomalies for manual auditing.

---

## 4b. Synthetic Test Case Generation Pipeline (V2 ✅)
- **Engine (`projects/evalops/src/generation/synthetic.py`):** Takes an `AgentDefinition` (`system_prompt`, `role`), invokes LiteLLM / Model Registry, and generates 10-20 attributed test cases (`EvalTestCase`) saved into `EvalTestSuite`.
- **API Endpoint:** `POST /api/evals/generate` schedules dataset creation.
- **API Endpoint:** `POST /api/evals/run` dispatches asynchronous evaluation jobs to Kafka.

---

## 5. Trace Observability & Diagnostics (Pending)
- **OpenTelemetry → Jaeger:** Gateway and Inference servers export spans. Jaeger UI at `http://localhost:16686`, OTLP gRPC at `http://localhost:4317`.
- **LangSmith Integration:** LLM-specific tracing (prompt debugging, token usage). Toggle via `LANGSMITH_TRACING=true`.
- **Waterfall Timeline Diagnostics:** Nested timeline spans showing OCR latency, embedding times, LiteLLM switchover, VRAM cold-starts.

---

## 6. CI/CD Integration (Pending)
- **GitHub Actions** (`.github/workflows/evalops.yml`): Trigger on PRs to `main`.
  1. `poetry install --all-extras`
  2. `ruff check .` + `mypy .`
  3. `pytest projects/evalops/tests/test_safety.py -v`
  4. Optional: `pytest projects/evalops/tests/test_benchmarks.py -v`
- **Test Data:** Fixtures in `tests/fixtures/`, version controlled, documented JSON schemas.

---

## 7. EvalOps as Optional Service (Pending)
- QA/CI tool, not production-critical.
- In docker-compose: behind `test` profile.
- Kafka consumers only start when explicitly enabled.
- Requires: `poetry install --extras "evalops"`.

---

## 8. Dependencies (`pyproject.toml` evalops extras)
```toml
evalops = [
    "deepeval (>=4.0.5,<5.0.0)",
    "ragas (>=0.2.0,<1.0.0)",
    "confluent-kafka (>=2.4.0,<3.0.0)",
]
```
