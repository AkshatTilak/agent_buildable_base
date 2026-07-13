# BT06: Kafka Async Streaming Pipeline

## Objective
Implement the Kafka-based asynchronous document ingestion pipeline with producer (API-side) and consumer (worker-side) components.

## Business/System Value
Decouples heavy document processing from the API thread, preventing timeouts and enabling horizontal scaling of document workers.

## Subtask Registry
- [ ] `sub/ST06_01_kafka_producer.md` — Implement Kafka producer for document.ingestion.events
- [ ] `sub/ST06_02_kafka_consumer.md` — Implement Kafka consumer worker with RAGFlow processing
- [ ] `sub/ST06_03_kafka_integration.md` — Wire producer into document upload endpoint, test end-to-end

## Complexity Rating
**Medium** — Async event streaming with careful error handling and status tracking.

## Source Code Location
`../../echomind-core/app/streaming/`
