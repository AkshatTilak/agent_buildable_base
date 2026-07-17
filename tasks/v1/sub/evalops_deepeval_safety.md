# Subtask: EvalOps DeepEval Safety Assertions

**Parent Link:** `base/evalops_qa.md`

## Actionable Steps
- [x] Configure DeepEval judge model to use `gemini/gemini-3.5-flash` (via LiteLLM) instead of default `gpt-4o`. Add `DEEPEVAL_MODEL` setting.
- [x] Implement Toxicity metric check: assert toxicity < 0.1 for typical prompts.
- [x] Implement Prompt Injection scanner: test against payloads from `tests/fixtures/injection_payloads.json`.
- [x] Implement PII Leakage detector: verify no PII in orchestrator output.

## Dependencies
- `deepeval` package. `OPENAI_API_KEY` or `GOOGLE_API_KEY`. Test fixture files.

## Definition of Done
- Safety test suite passes in CI. Injection attempts blocked. PII not leaked.
