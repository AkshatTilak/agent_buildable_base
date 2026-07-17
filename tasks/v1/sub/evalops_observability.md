# Subtask: EvalOps Observability Integration

**Parent Link:** `base/evalops_qa.md`

## Actionable Steps
- [x] Configure Gateway and Inference servers to export spans to Jaeger (OTLP gRPC at `http://localhost:4317`).
- [x] Configure LangSmith integration for LLM-specific tracing (toggle via `LANGSMITH_TRACING=true`).
- [x] Implement waterfall timeline diagnostics: nested spans showing OCR latency, embedding times, LiteLLM switchover, VRAM cold-starts.

## Dependencies
- `opentelemetry` packages. Jaeger container running. `LANGSMITH_API_KEY` for LangSmith.

## Definition of Done
- Traces visible in Jaeger UI. LangSmith toggle works. Waterfall spans identify bottlenecks.
