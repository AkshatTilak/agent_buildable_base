# Subtask: LLM Completion Provider Configuration

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] Configure all completion providers in Model Registry: Google AI, Groq, OpenRouter, Cerebras, Ollama.
- [ ] Implement default fallback chain configuration in registry.
- [ ] Implement capability-aware context truncation:
  - Frontier models (1M+): no truncation.
  - Mid-tier (70B): truncate to 32,000 tokens.
  - Small free-tier: truncate to 8,000 tokens.
- [ ] Truncation in **tokens** (not characters) via `tiktoken` or LiteLLM's token counter.
- [ ] Ensure all provider API key requirements are documented and validated on startup.

## Dependencies
- `litellm`, `tiktoken` packages.
- API keys: `GOOGLE_API_KEY`, `OPENROUTER_API_KEY`, `GROQ_API_KEY`, `CEREBRAS_API_KEY`.

## Definition of Done
- All providers configurable and switchable via `COMPLETION_MODEL` env var.
- Fallback chain triggers automatically on provider failure.
- Context truncation adjusts payload size per model tier.
