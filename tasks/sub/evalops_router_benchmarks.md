# Subtask: EvalOps Router & Logic Benchmarks

**Parent Link:** `base/evalops_qa.md`

## Actionable Steps
- [ ] Implement `bench_gguf.py`: evaluate classifier F1-score against `tests/fixtures/grpo_eval_data.json`. Track cold-start vs warm latencies. Assert VRAM freed after idle timeout.
- [ ] Implement `bench_mmlu.py`: run GSM8k, HumanEval, MMLU subsets against synthesis node. Test provider failure injection → verify fallback chain.
- [ ] Implement `bench_models.py` (NEW): run benchmarks across model options per role. Compare accuracy, latency, VRAM. Output comparison table.

## Dependencies
- Active classifier model. Test fixtures. Inference server running.

## Definition of Done
- All three benchmark scripts execute successfully. Comparison table generated for model selection.
