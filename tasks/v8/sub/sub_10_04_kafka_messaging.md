# Subtask 10_04: Kafka Messaging Live Tests

## Parent Base Task
`tasks/v8/base/10_live_api_tests.md`

## Objective
Create `tests/live_api/test_kafka_messaging.py` — Kafka publish/consume & offline fallback.

## Tasks
1. [ ] Publish ingestion job to Kafka topic → verify SyntraFlow consumer picks it up.
2. [ ] Publish eval trigger → verify EvalOps consumer processes it.
3. [ ] Test Kafka-offline fallback (stop Kafka → verify local background execution takes over).

## Definition of Done
- Kafka publish/consume verified for ingestion and eval triggers.
- Kafka-offline fallback verified (local background execution takes over).
