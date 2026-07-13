# System Topology — EchoMind Core

## Architecture Overview

```mermaid
graph TB
    subgraph Frontend["Frontend (React SPA)"]
        UI["React Client"]
    end

    subgraph API["API Layer"]
        FA["FastAPI Server<br/>:8000"]
    end

    subgraph AI["AI Orchestration"]
        LC["LangChain Agent"]
        LF["Langflow Visual Builder<br/>:7860"]
    end

    subgraph RAG["Document Intelligence"]
        RF["RAGFlow Engine<br/>:9380"]
    end

    subgraph Data["Data Layer"]
        PG["PostgreSQL + pgvector<br/>:5432"]
        RD["Redis Cache<br/>:6379"]
    end

    subgraph Streaming["Event Streaming"]
        KF["Apache Kafka<br/>:9092"]
        CW["Consumer Workers"]
    end

    subgraph Analytics["Batch Analytics"]
        SM["Spark Master<br/>:7077"]
        SW["Spark Worker"]
    end

    subgraph Observability["Observability"]
        LFU["Langfuse Server<br/>:3000"]
    end

    UI -->|REST/WS| FA
    FA -->|Invoke| LC
    FA -->|/v1/run/flow_id| LF
    LC -->|Tool Calls| PG
    LC -->|Tool Calls| RF
    LC -->|Chat History| RD
    FA -->|Produce Events| KF
    KF -->|Consume| CW
    CW -->|Parse Docs| RF
    CW -->|Store Embeddings| PG
    SM --> SW
    SW -->|JDBC Read/Write| PG
    LC -.->|Traces| LFU
    LF -.->|Traces| LFU
    LFU -->|Store| PG
    FA -->|Session Cache| RD
```

## Service Topology

| Service | Container Name | Image | Port | Depends On |
|---|---|---|---|---|
| FastAPI App | echomind-app | Custom Dockerfile | 8000 | postgres, redis, kafka |
| PostgreSQL | echomind-postgres | pgvector/pgvector:pg16 | 5432 | — |
| Redis | echomind-redis | redis:7-alpine | 6379 | — |
| Kafka | echomind-kafka | bitnami/kafka:3.7 | 9092 | — |
| Langfuse | echomind-langfuse | langfuse/langfuse:2 | 3000 | postgres |
| Langflow | echomind-langflow | langflowai/langflow:latest | 7860 | postgres |
| RAGFlow | echomind-ragflow | infiniflow/ragflow:latest | 9380 | — |
| Spark Master | echomind-spark-master | bitnami/spark:3.5 | 8080, 7077 | — |
| Spark Worker | echomind-spark-worker | bitnami/spark:3.5 | — | spark-master |

## Network

All services communicate on the `echomind-net` Docker bridge network.
