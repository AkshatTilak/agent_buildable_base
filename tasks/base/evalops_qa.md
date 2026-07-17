# Base Task: EvalOps QA Pipeline

## Objective
Implement the full EvalOps quality assurance pipeline: RAGAS retrieval benchmarks, DeepEval safety assertions, router/logic benchmarks, Kafka consumers for trace analysis, observability integrations, and CI/CD workflow.

## Business/System Value
EvalOps is the system's immune system. Without automated benchmarks and safety checks, there's no way to catch regressions, hallucinations, or security vulnerabilities before they reach users.

## Subtask Registry
- [x] `sub/evalops_ragas_benchmarks.md`
- [x] `sub/evalops_deepeval_safety.md`
- [x] `sub/evalops_router_benchmarks.md`
- [x] `sub/evalops_kafka_consumers.md`
- [x] `sub/evalops_observability.md`
- [x] `sub/evalops_cicd.md`
- [x] `sub/evalops_service_config.md`

## Complexity Rating
**High** — Requires integration with multiple evaluation frameworks (DeepEval, RAGAS), Kafka consumers, OpenTelemetry, and CI/CD pipeline configuration.

## References
- `references/logic/evalops.md` — EvalOps architecture and specs.
- `references/logic/model_registry.md` — Model options for benchmarking.
