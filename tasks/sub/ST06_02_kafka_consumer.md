# ST06_02: Implement Kafka Consumer Worker

## Parent Link
`base/BT06_kafka_streaming.md`

## Actionable Steps
- [x] Step 1: Initialize AIOKafkaConsumer subscribing to `document.ingestion.events`
- [x] Step 2: Implement message processing loop with RAGFlow document handling
- [x] Step 3: Generate embeddings and insert into vector_knowledge table
- [x] Step 4: Update document status (PENDING → PROCESSING → COMPLETE/FAILED)
- [x] Step 5: Add error handling, retry logic, and dead letter queue for failures
- [ ] Step 6: Add Langfuse tracing to consumer processing

## Dependencies
- `ST05_01_ragflow_client.md` — RAGFlow client
- `ST02_01_init_db_sql.md` — Database tables

## Definition of Done
Consumer processes documents end-to-end, handles errors gracefully, status tracking works.
