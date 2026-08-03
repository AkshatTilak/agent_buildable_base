# Subtask: Remove Default Initialization (Clean State)

- Update `gateway/main.py` and `gateway/core/setup.py` to stop seeding default hubs, default agents, or any mock data on startup.
- The platform should start fully empty and rely strictly on user actions (or the env-driven super admin creation) to populate data.
