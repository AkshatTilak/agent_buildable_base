# Subtask: Global System Status Panel

**Parent Link:** `base/frontend_ui.md`

## Actionable Steps
- [ ] Build connection grid: health indicators for Gateway, Inference, Postgres, Qdrant, Neo4j, Redis, Kafka.
- [ ] Build VRAM monitor: visual gauge (used/budget), loaded model slots, cold-start vs warm latency.
- [ ] Build Model Registry panel: active model per role, local/cloud indicator, VRAM impact, benchmark links.
- [ ] Add admin quick-console links: Qdrant, Neo4j, pgAdmin, Kafka UI, Jaeger.

## Dependencies
- Gateway and Inference `/health` endpoints. Model Registry API.

## Definition of Done
- All service statuses visible. VRAM gauge updates live. Model registry panel shows current config.
