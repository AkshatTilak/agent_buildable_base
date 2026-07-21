# Subtask: Evaluation Synthetic Test Generation

**Parent Link:** `base/05_eval_test_generation.md`

## Actionable Steps
- [x] Create a Python utility `projects/evalops/src/generation/synthetic.py`.
- [x] Build a prompt pipeline that takes an agent's `system_prompt` and `role` description, calls a registry LLM, and prompts it to generate 10-20 diverse, realistic user queries matching the agent's target domain.
- [x] Feed documents ingested via SyntraFlow (or retrieves vectors from Qdrant) as reference contexts to generate high-fidelity ground truth outputs alongside the synthetic questions.
- [x] Implement an API endpoint `/api/evals/generate` that schedules this generation process and saves the results to `EvalTestCase`.

## Dependencies
- `sub/eval_attribution_schema.md`

## Definition of Done
- LLM utility successfully parses agent prompts and returns a list of synthetically generated input-output-context test tuples.
- DB records are written without errors.
