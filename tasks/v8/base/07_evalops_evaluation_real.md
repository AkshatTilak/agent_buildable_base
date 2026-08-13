# Base Task 7: Real-World Integration Tests — EvalOps (Evaluation Pipelines) (v8)

## Objective
Test evaluation dataset management, DeepEval/RAGAS runner execution, and eval-hub management against real services.

## Why
EvalOps is the automated QA layer. Existing tests mock the DeepEval/RAGAS runners and never validate real metric computation against a real LLM judge or real Qdrant retrieval data.

## Scope
- **`tests/integration/evalops/test_datasets_real.py`** — Dataset management:
  - Create eval dataset → add test cases → list/get/delete.
  - Dataset import from JSON fixtures.
  - Dataset-hub scoping.
- **`tests/integration/evalops/test_eval_runner_real.py`** — Eval execution:
  - Run DeepEval safety tests (hallucination, injection, toxicity) against a real LLM response using `gemini/gemini-3.5-flash` as `DEEPEVAL_MODEL`.
  - Run RAGAS retrieval quality benchmarks against real Qdrant collection data (embeddings from `harrier-0.6b`).
  - Eval run lifecycle (dispatch → in-progress → completed with scores).
  - Eval results storage and retrieval.
- **`tests/integration/evalops/test_eval_hub_real.py`** — Eval hub management:
  - Eval hub CRUD.
  - Link eval hub to agent hub → run evals against agent's model.
  - Eval dashboard metrics aggregation.

## Associated Subtasks
1. `[ ]` `sub_07_01_datasets_real.md`: `test_datasets_real.py` — dataset management & import.
2. `[ ]` `sub_07_02_eval_runner_real.md`: `test_eval_runner_real.py` — DeepEval/RAGAS execution with real judge.
3. `[ ]` `sub_07_03_eval_hub_real.md`: `test_eval_hub_real.py` — eval hub management & dashboard metrics.

## Definition of Done
- Dataset create/list/get/delete and JSON import verified against real Postgres.
- DeepEval safety tests and RAGAS benchmarks run with real LLM judge and real Qdrant data, producing scores.
- Eval run lifecycle and results storage/retrieval verified.
- Eval hub CRUD, agent-hub linking, and dashboard metrics aggregation verified.
