# Subtask: EvalOps RAGAS Benchmarks

**Parent Link:** `base/evalops_qa.md`

## Actionable Steps
- [ ] Implement Context Recall verification: measure overlap of retrieved segments with ground truth.
- [ ] Implement Faithfulness metric: verify synthesis output is grounded in gathered context.
- [ ] Implement Semantic Similarity scoring: compare synthesized output to ground truths using active embedding model.

## Dependencies
- `ragas` package. Active embedding model from Model Registry.

## Definition of Done
- All three RAGAS metrics produce scores against a test dataset. Tests runnable via `pytest`.
