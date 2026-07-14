# Subtask: EvalOps Dashboard

**Parent Link:** `base/frontend_ui.md`

## Actionable Steps
- [ ] Build RAGAS metric charts: timeline graphs for Context Recall, Faithfulness, Semantic Similarity.
- [ ] Build safety audit logs: blocked injections, toxicity ratings, PII alerts.
- [ ] Build diagnostic trace waterfall: OpenTelemetry span visualization with bottleneck highlighting.
- [ ] Build model comparison dashboard: benchmark results from `bench_models.py`.

## Dependencies
- EvalOps API endpoints. `recharts` or `chart.js` library.

## Definition of Done
- All four EvalOps panels render data. Charts update with new benchmark runs.
