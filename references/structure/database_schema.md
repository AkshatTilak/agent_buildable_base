# Database Schema — EchoMind Core

## Entity Relationship Diagram

```mermaid
erDiagram
    roles ||--o{ users : "has_role"
    users ||--o| user_profiles : "has_profile"
    users ||--o| user_analytics : "has_analytics"
    users ||--o{ documents : "uploads"
    documents ||--o{ vector_knowledge : "has_chunks"

    roles {
        int id PK
        varchar role_name UK
        jsonb permissions
    }

    users {
        uuid id PK
        varchar email UK
        varchar password_hash
        int role_id FK
        timestamptz created_at
        timestamptz updated_at
    }

    user_profiles {
        uuid user_id PK_FK
        jsonb preferences
        varchar usage_tier
    }

    user_analytics {
        uuid user_id PK_FK
        int total_interactions
        jsonb top_topics
        float avg_session_minutes
        timestamptz last_updated
    }

    documents {
        uuid id PK
        uuid user_id FK
        varchar file_name
        varchar file_path
        bigint file_size
        varchar mime_type
        varchar status
        timestamptz created_at
        timestamptz updated_at
    }

    vector_knowledge {
        uuid id PK
        uuid doc_id FK
        text chunk_text
        int chunk_index
        vector_3072 embedding
        jsonb metadata
        timestamptz created_at
    }
```

## Indexes

| Table | Index | Type | Purpose |
|---|---|---|---|
| vector_knowledge | idx_vector_knowledge_embedding | HNSW (m=16, ef_construction=64) | Cosine similarity search |
| vector_knowledge | idx_vector_knowledge_doc_id | B-Tree | Document-level chunk lookups |
| documents | idx_documents_user_id | B-Tree | User document listings |
| documents | idx_documents_status | B-Tree | Status-based queries |

## Default Roles (Seeded)

| Role | Rate Limit | Tools |
|---|---|---|
| standard | 10/min | vector_search, ragflow_retrieval |
| premium | 50/min | vector_search, ragflow_retrieval, user_analytics |
| admin | 100/min | All tools + diagnostics + user_management |

## Vector Configuration

- **Embedding Model**: text-embedding-3-large
- **Dimension**: 3072
- **Distance Metric**: Cosine similarity (`<=>` operator)
- **Index**: HNSW (Hierarchical Navigable Small World)
- **Recommended**: `maintenance_work_mem = '8GB'` for HNSW build performance
