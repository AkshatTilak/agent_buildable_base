# ST06_01: Implement Kafka Producer

## Parent Link
`base/BT06_kafka_streaming.md`

## Actionable Steps
- [ ] Step 1: Initialize AIOKafkaProducer with JSON serialization
- [ ] Step 2: Implement `publish_document_event()` publishing to `document.ingestion.events`
- [ ] Step 3: Implement graceful startup/shutdown lifecycle
- [ ] Step 4: Wire into FastAPI lifespan manager
- [ ] Step 5: Test message production to Kafka topic

## Dependencies
- `aiokafka` installed, Kafka container running

## Definition of Done
Producer publishes JSON events to Kafka topic, graceful lifecycle management verified.
