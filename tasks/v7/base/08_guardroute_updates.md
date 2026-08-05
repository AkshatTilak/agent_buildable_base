# Base Task 08: GuardRoute Stabilizations (v7)

## Objective
Enhance GuardRoute for model selection reliability, ensuring dynamic model provisioning works flawlessly based on available API keys.

## Subtasks
1. `[x]` `sub_08_01_dynamic_model_registry.md`: Update `registry.py` to dynamically fetch all models from Litellm.
2. `[x]` `sub_08_02_api_key_awareness.md`: Ensure gateway filters models based on existing API keys.
3. `[x]` `sub_08_03_frontend_model_selection.md`: Update all model dropdowns in the frontend to disable unselectable models and dynamically fetch LiteLLM models.
4. `[x]` `sub_08_04_local_disk_path_and_purging.md`: Implement local model disk storage path resolution, disk existence status checking (`is_downloaded`), model deletion with optional disk purging (`DELETE /api/models/local/{model_id}?purge_disk=true`), HuggingFace credential support (`HF_TOKEN`), and Harrier 0.6B local embedder loader integration.
