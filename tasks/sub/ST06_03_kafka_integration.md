# ST06_03: Kafka Integration Testing

## Parent Link
`base/BT06_kafka_streaming.md`

## Actionable Steps
- [ ] Step 1: Wire producer into POST /api/v1/documents/upload endpoint
- [ ] Step 2: Verify upload returns 202 Accepted with document_id
- [ ] Step 3: Verify consumer picks up message and processes document
- [ ] Step 4: Verify document status transitions (PENDING → PROCESSING → COMPLETE)
- [ ] Step 5: Test error scenarios (invalid file, RAGFlow failure, Kafka downtime)

## Dependencies
- `ST06_01_kafka_producer.md`, `ST06_02_kafka_consumer.md`

## Definition of Done
End-to-end document ingestion pipeline verified from upload to vector storage.
