# Subtask 08_03: Frontend Model Selection Dropdown UI Enhancements

## Objective
Update model selection dropdown menus across Playground, Agent Hub, and Workflow Builder to visually disable missing-key models with informative tooltips.

## Tasks
1. `[x]` Update shared `ModelSelector.tsx` component to consume model availability status flags.
2. `[x]` Visually disable unavailable models in dropdown menus and display hover tooltips explaining missing API key requirements.
3. `[x]` Integrate dynamic available models discovery calling LiteLLM endpoints in shared `ModelSelector.tsx` for Cloud execution mode.
4. `[x]` Add dynamic warning banners to inform users if provider API keys are missing.
5. `[x]` Test frontend model selector across Playground, Hubs, and Workflow Builder UI.
