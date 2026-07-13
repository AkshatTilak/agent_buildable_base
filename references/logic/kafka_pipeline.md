# Kafka Document Ingestion Pipeline — EchoMind Core

## Pipeline Flow

```mermaid
sequenceDiagram
    participant U as User
    participant FA as FastAPI
    participant DB as PostgreSQL
    participant KF as Kafka
    participant CW as Consumer Worker
    participant RF as RAGFlow
    participant PG as pgvector

    U->>FA: POST /documents/upload (file)
    FA->>FA: Validate file (size, type)
    FA->>FA: Save to upload volume
    FA->>DB: INSERT documents (status=PENDING)
    FA->>KF: Produce {doc_id, user_id, file_path}
    FA-->>U: 202 Accepted {document_id}

    Note over KF,CW: Async processing (API thread freed)

    KF->>CW: Consume message
    CW->>DB: UPDATE documents (status=PROCESSING)
    CW->>RF: Upload & parse document
    RF-->>CW: Parsed chunks
    CW->>CW: Generate embeddings
    CW->>PG: INSERT vector_knowledge (chunks + embeddings)
    CW->>DB: UPDATE documents (status=COMPLETE)

    Note over CW: On failure:
    CW->>DB: UPDATE documents (status=FAILED)
    CW->>KF: Log to dead letter queue
```

## Kafka Configuration

- **Topic**: `document.ingestion.events`
- **Consumer Group**: `document-processor`
- **Auto Offset Reset**: `earliest`
- **Serialization**: JSON (UTF-8)

## Event Payload Schema

```json
{
    "document_id": "uuid",
    "user_id": "uuid",
    "file_path": "/uploads/user_uuid/filename.pdf",
    "action": "INGEST"
}
```

## Error Handling

1. **Transient failures** (network, timeout): Retry with exponential backoff
2. **Permanent failures** (corrupt file, parse error): Move to dead letter queue, set status=FAILED
3. **Kafka unavailable**: FastAPI returns 503 Service Unavailable
