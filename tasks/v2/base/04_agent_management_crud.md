# Base Task: Agent Management (CRUD)

## Objective
Build a dedicated "Agent Hub" that provides visibility into all active agents and allows for full CRUD (Create, Read, Update, Delete) capabilities over agent properties and tooling.

## Business/System Value
Operators currently have no visibility into what agents exist or how they are configured without reading code. The Agent Hub will allow operators to view all agents, edit their system prompts, change which LLM model they use from the Model Registry, and toggle available tools (like search, python execution).

## Complexity Rating
Medium (Building standard CRUD APIs backed by Postgres/Redis, with corresponding Frontend UI).

## Subtask Registry
* `[x] sub/agent_crud_schema.md` — Define Pydantic models and SQLAlchemy schemas for `AgentDefinition` (Prompts, Model ID, Tools, Hyperparams).
* `[x] sub/agent_crud_api.md` — Build standard REST APIs in the Gateway for listing, creating, and updating Agent configurations.
* `[x] sub/agent_hub_ui.md` — Build the Frontend "Agent Hub" grid and detail view for managing agents.
* `[x] sub/agent_runtime_sync.md` — Ensure that when an Agent is updated in the DB, the running Inference Server and GuardRoute orchestrator reload its configuration.
