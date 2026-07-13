# BT09: Langflow Visual Agent Builder Integration

## Objective
Deploy Langflow as a visual agent creation platform with PostgreSQL persistence and native Langfuse observability, callable via REST API.

## Business/System Value
Enables no-code/low-code agent prototyping alongside the code-based LangChain approach, fulfilling the dual-paradigm agent creation requirement.

## Subtask Registry
- [ ] `sub/ST09_01_langflow_deploy.md` — Validate Langflow Docker deployment with shared PostgreSQL
- [ ] `sub/ST09_02_langflow_api.md` — Implement /v1/run/{flow_id} integration from FastAPI
- [ ] `sub/ST09_03_langflow_langfuse.md` — Configure native Langfuse observability in Langflow

## Complexity Rating
**Low-Medium** — Primarily configuration and API integration.

## Source Code Location
`../../echomind-core/docker-compose.yml` (Langflow service)
