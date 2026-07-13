# Environment Variables Reference — EchoMind Core

## Variable Catalog

### PostgreSQL
| Variable | Required | Default | Description |
|---|---|---|---|
| `POSTGRES_USER` | Yes | `echomind` | Database username |
| `POSTGRES_PASSWORD` | Yes | `echomind_secret` | Database password |
| `POSTGRES_DB` | Yes | `echomind_db` | Database name |
| `DATABASE_URL` | Yes | — | Async SQLAlchemy connection string |

### Redis
| Variable | Required | Default | Description |
|---|---|---|---|
| `REDIS_URL` | Yes | `redis://redis:6379/0` | Redis connection URL |

### Kafka
| Variable | Required | Default | Description |
|---|---|---|---|
| `KAFKA_BOOTSTRAP_SERVERS` | Yes | `kafka:9092` | Kafka broker address |

### JWT Authentication
| Variable | Required | Default | Description |
|---|---|---|---|
| `JWT_SECRET_KEY` | Yes | — | Secret for JWT signing (generate a strong random string) |
| `JWT_ALGORITHM` | No | `HS256` | JWT signing algorithm |
| `JWT_ACCESS_TOKEN_EXPIRE_MINUTES` | No | `30` | Token expiration time |

### Google Gemini
| Variable | Required | Default | Description |
|---|---|---|---|
| `GOOGLE_API_KEY` | Yes | — | Google AI API key for Gemini |

### Langfuse
| Variable | Required | Default | Description |
|---|---|---|---|
| `LANGFUSE_SECRET_KEY` | Yes | — | Langfuse server secret key |
| `LANGFUSE_PUBLIC_KEY` | Yes | — | Langfuse server public key |
| `LANGFUSE_BASE_URL` | Yes | `http://langfuse-server:3000` | Langfuse server URL |
| `NEXTAUTH_SECRET` | Yes | — | NextAuth secret for Langfuse web UI |
| `SALT` | Yes | — | Encryption salt for Langfuse |

### RAGFlow
| Variable | Required | Default | Description |
|---|---|---|---|
| `RAGFLOW_API_URL` | Yes | `http://ragflow:9380` | RAGFlow engine URL |
| `RAGFLOW_API_KEY` | Yes | — | RAGFlow API key |

### PySpark
| Variable | Required | Default | Description |
|---|---|---|---|
| `SPARK_MASTER_URL` | Yes | `spark://spark-master:7077` | Spark master URL |
| `SPARK_JDBC_URL` | Yes | — | JDBC PostgreSQL URL (non-async) |
| `SPARK_JDBC_USER` | Yes | — | JDBC username |
| `SPARK_JDBC_PASSWORD` | Yes | — | JDBC password |

### Application
| Variable | Required | Default | Description |
|---|---|---|---|
| `APP_NAME` | No | `EchoMind Core` | Application display name |
| `APP_ENV` | No | `development` | Environment (development/staging/production) |
| `APP_DEBUG` | No | `true` | Debug mode |
| `LOG_LEVEL` | No | `INFO` | Logging level |
| `CORS_ORIGINS` | No | `["http://localhost:3000"]` | Allowed CORS origins |

## Human Action Required

> **API Keys**: The following keys must be manually obtained and set:
> - `GOOGLE_API_KEY` — From [Google AI Studio](https://aistudio.google.com/)
> - `RAGFLOW_API_KEY` — Generated after first RAGFlow login
> - `LANGFUSE_SECRET_KEY` / `LANGFUSE_PUBLIC_KEY` — Generated after first Langfuse login
