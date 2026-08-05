# Subtask 08_02: API Key Awareness & Route Filtering

## Objective
Filter available models based on active environment API keys (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, etc.) and annotate models with availability status.

## Tasks
1. `[x]` Implement API key inspector in `projects/guardroute/services/key_inspector.py`.
2. `[x]` Annotate model entries in registry output with status flags (`ready`, `missing_key`, `local_only`).
3. `[x]` Filter model routing targets to prevent routing requests to providers without configured keys.
4. `[x]` Add unit test suite in `tests/test_key_awareness.py`.
