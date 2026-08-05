# Subtask 09_01: EvalOps Judge Models Registry Sync

## Objective
Sync DeepEval and Ragas evaluation runner target models with GuardRoute dynamic model registry and implement graceful local fallback when remote judge keys are missing.

## Tasks
1. `[x]` Update evaluation runner in `projects/evalops/runners/judge_resolver.py` to query GuardRoute model registry.
2. `[x]` Validate judge model availability prior to launching eval benchmarks.
3. `[x]` Fallback automatically to default local judge model (e.g. Ollama/Harrier) if remote API judge key is missing.
4. `[x]` Verify judge model resolution in `tests/test_eval_judge_sync.py`.
