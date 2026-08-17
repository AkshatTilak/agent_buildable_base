# Subtask 10_04: Kafka Messaging Live Tests

## Parent Base Task
`tasks/v8/base/10_live_api_tests.md`

## Objective
Create `tests/live_api/test_kafka_messaging.py` — Kafka publish/consume & offline fallback.

## Tasks
1. [x] Publish ingestion job to Kafka topic → verify SyntraFlow consumer picks it up.
2. [x] Publish eval trigger → verify EvalOps consumer processes it.
3. [x] Test Kafka-offline fallback (stop Kafka → verify local background execution takes over).

## Definition of Done
- Kafka publish/consume verified for ingestion and eval triggers on actual Kafka broker (`:9092`).
- Kafka-offline fallback verified (local background execution takes over).
- If errors arise, inspect Docker container logs (`docker compose logs kafka`) and fix root causes directly in Kafka consumer/producer code.
- Deleting test data is not necessary.
