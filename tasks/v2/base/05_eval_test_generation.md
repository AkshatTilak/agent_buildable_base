# Base Task: Automated Evaluation & Test Generation

## Objective
Integrate EvalOps directly with the Agent creation lifecycle, allowing operators to automatically generate or manually define test datasets specifically attributed to individual agents.

## Business/System Value
Evals are currently disconnected scripts. When an operator edits an agent (e.g., changes its prompt), they need to know if the agent degraded. By attributing test cases directly to an Agent ID, the system can automatically run a suite of tests (via RAGAS or DeepEval) targeting that specific agent whenever it is modified.

## Complexity Rating
High (Requires integrating LLM-based synthetic data generation and piping test results back to the frontend Agent UI).

## Subtask Registry
* `[x] sub/eval_attribution_schema.md` — Create DB schemas mapping `AgentID` to a `TestSuite` (Collection of queries and expected contexts/answers).
* `[x] sub/eval_synthetic_generation.md` — Create a background pipeline that uses an LLM to automatically generate 10-20 test queries based on an Agent's System Prompt and connected Tools.
* `[x] sub/eval_runner_integration.md` — Build an API that triggers RAGAS/DeepEval for a specific AgentID's test suite and stores the metrics (Faithfulness, Answer Relevance).
* `[x] sub/eval_frontend_attribution.md` — Add an "Evaluations" tab inside the Frontend Agent Hub UI to view test results and manually edit the test queries.
