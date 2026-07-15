# ST06_03: Kafka Integration Testing

## Parent Link
`base/BT06_kafka_streaming.md`

## Actionable Steps
- [x] Step 1: Wire producer into POST /api/v1/documents/upload endpoint
- [x] Step 2: Verify upload returns 202 Accepted with document_id
- [x] Step 3: Verify consumer picks up message and processes document
- [x] Step 4: Verify document status transitions (PENDING → PROCESSING → COMPLETE)
- [x] Step 5: Test error scenarios (invalid file, RAGFlow failure, Kafka downtime)

## Dependencies
- `ST06_01_kafka_producer.md`, `ST06_02_kafka_consumer.md`

## Definition of Done
End-to-end document ingestion pipeline verified from upload to vector storage.
