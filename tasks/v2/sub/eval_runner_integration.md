# Subtask: Evaluation Runner Integration (Kafka-Driven)

**Parent Link:** `base/05_eval_test_generation.md`

## Actionable Steps
- [ ] Implement an API endpoint `/api/evals/run` that accepts `agent_id`, creates a pending `EvalRunHistory` record, and publishes an `agent-eval-trigger` event to Kafka.
- [ ] Build a background Kafka consumer in EvalOps (`projects/evalops/src/runner/consumer.py`) that subscribes to `agent-eval-trigger`.
- [ ] The consumer must:
  - [ ] Fetch the agent configuration and its `EvalTestCase` collection.
  - [ ] Invoke the agent's target model/workflow for each test query to collect outputs.
  - [ ] Run RAGAS and DeepEval evaluation metrics (Faithfulness, Answer Relevance) comparing results with expected answers.
  - [ ] Update the `EvalRunHistory` table with computed scores and execution metrics.

## Dependencies
- `sub/eval_synthetic_generation.md`

## Definition of Done
- Evaluations execute asynchronously without locking HTTP connections.
- Final RAGAS/DeepEval test metric results are correctly written back to PostgreSQL upon run completion.
