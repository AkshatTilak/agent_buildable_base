# Subtask: GuardRoute LLM Fallbacks

**Parent Link:** `base/guardroute_orchestrator.md`

## Actionable Steps
- [x] Multi-provider fallback chain configuration using LiteLLM.
- [x] Fallback sequence: Gemini -> Groq -> OpenRouter -> Last resort.

## Definition of Done
- API rates limit triggers or outages successfully fall back to downstream providers without crashing request.
