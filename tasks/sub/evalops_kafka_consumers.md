# Subtask: EvalOps Kafka Consumers

**Parent Link:** `base/evalops_qa.md`

## Actionable Steps
- [ ] Implement background consumer daemons subscribing to `guardroute-traces` and `syntraflow-ingestion-jobs` Kafka topics.
- [ ] Process traces asynchronously: extract latency stats, token counts, completion text structures.
- [ ] Write metrics to `evalops_reports` PostgreSQL table. Flag anomalies for manual auditing.
- [ ] Add `confluent-kafka` to `evalops` optional dependency group.

## Dependencies
- `confluent-kafka` package. Kafka broker running. PostgreSQL `evalops_reports` table.

## Definition of Done
- Consumers process messages from both topics. Reports written to database. Anomalies flagged.
