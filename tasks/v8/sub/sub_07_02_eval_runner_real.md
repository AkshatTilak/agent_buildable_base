# Subtask 07_02: Eval Runner Real Integration Tests

## Parent Base Task
`tasks/v8/base/07_evalops_evaluation_real.md`

## Objective
Create `tests/integration/evalops/test_eval_runner_real.py` — DeepEval/RAGAS execution with real judge.

## Tasks
1. [ ] Run DeepEval safety tests (hallucination, injection, toxicity) against a real LLM response using `gemini/gemini-3.5-flash` as `DEEPEVAL_MODEL`.
2. [ ] Run RAGAS retrieval quality benchmarks against real Qdrant collection data (embeddings from `harrier-0.6b`).
3. [ ] Eval run lifecycle (dispatch → in-progress → completed with scores).
4. [ ] Eval results storage and retrieval.

## Definition of Done
- DeepEval safety tests and RAGAS benchmarks run with real LLM judge and actual running Qdrant (`:6333`) data, producing scores.
- Eval run lifecycle and results storage/retrieval verified.
- If errors arise, inspect Docker container logs (`docker compose logs db qdrant`) and fix root causes directly in EvalOps runner logic.
- Deleting test data is not necessary.
