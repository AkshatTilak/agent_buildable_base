# Subtask 08_01: GuardRoute Dynamic Model Registry Sync

## Objective
Update `projects/guardroute/registry.py` to dynamically discover models from LiteLLM model map with metadata caching and standard capability schema.

## Tasks
1. `[x]` Update `guardroute/registry.py` to pull model schemas dynamically from LiteLLM model registry.
2. `[x]` Implement caching layer for model capabilities (context window length, streaming, tool use support) with TTL.
3. `[x]` Expose `/models/available` endpoint in Gateway returning model status schema.
4. `[x]` Add unit tests in `tests/test_model_registry.py`.
