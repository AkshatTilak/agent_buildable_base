# Technology Stack Reference — EchoMind Core

## Core Dependencies & Documentation

| Technology | Version | Documentation | Purpose |
|---|---|---|---|
| **Python** | 3.12+ | [docs.python.org](https://docs.python.org/3.12/) | Runtime |
| **FastAPI** | ^0.115 | [fastapi.tiangolo.com](https://fastapi.tiangolo.com/) | REST API + WebSocket |
| **Poetry** | 1.8+ | [python-poetry.org](https://python-poetry.org/docs/) | Dependency management |
| **SQLAlchemy** | ^2.0 | [docs.sqlalchemy.org](https://docs.sqlalchemy.org/en/20/) | Async ORM |
| **asyncpg** | ^0.30 | [magicstack.github.io/asyncpg](https://magicstack.github.io/asyncpg/) | PostgreSQL driver |
| **Alembic** | ^1.14 | [alembic.sqlalchemy.org](https://alembic.sqlalchemy.org/) | Database migrations |
| **pgvector** | ^0.3 | [github.com/pgvector/pgvector-python](https://github.com/pgvector/pgvector-python) | Vector storage |
| **Pydantic** | ^2.10 | [docs.pydantic.dev](https://docs.pydantic.dev/latest/) | Data validation |
| **LangChain** | ^0.3 | [python.langchain.com](https://python.langchain.com/docs/) | Agent orchestration |
| **LangChain Google GenAI** | ^2.0 | [python.langchain.com/docs/integrations](https://python.langchain.com/docs/integrations/) | Gemini LLM integration |
| **Langfuse** | ^2.50 | [langfuse.com/docs](https://langfuse.com/docs) | LLM observability |
| **Langflow** | latest | [docs.langflow.org](https://docs.langflow.org/) | Visual agent builder |
| **RAGFlow** | latest | [github.com/infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Document RAG engine |
| **ragflow-sdk** | ^0.15 | [pypi.org/project/ragflow-sdk](https://pypi.org/project/ragflow-sdk/) | Python SDK |
| **aiokafka** | ^0.12 | [aiokafka.readthedocs.io](https://aiokafka.readthedocs.io/) | Async Kafka client |
| **Redis** | ^5.2 | [redis-py.readthedocs.io](https://redis-py.readthedocs.io/) | Caching & sessions |
| **PySpark** | ^3.5 | [spark.apache.org/docs/latest/api/python](https://spark.apache.org/docs/latest/api/python/) | Batch analytics |
| **python-jose** | ^3.3 | [python-jose.readthedocs.io](https://python-jose.readthedocs.io/) | JWT tokens |
| **passlib** | ^1.7 | [passlib.readthedocs.io](https://passlib.readthedocs.io/) | Password hashing |

## Docker Images

| Image | Tag | Source |
|---|---|---|
| pgvector/pgvector | pg16 | [hub.docker.com/r/pgvector/pgvector](https://hub.docker.com/r/pgvector/pgvector) |
| redis | 7-alpine | [hub.docker.com/_/redis](https://hub.docker.com/_/redis) |
| bitnami/kafka | 3.7 | [hub.docker.com/r/bitnami/kafka](https://hub.docker.com/r/bitnami/kafka) |
| langfuse/langfuse | 2 | [hub.docker.com/r/langfuse/langfuse](https://hub.docker.com/r/langfuse/langfuse) |
| langflowai/langflow | latest | [hub.docker.com/r/langflowai/langflow](https://hub.docker.com/r/langflowai/langflow) |
| infiniflow/ragflow | latest | [hub.docker.com/r/infiniflow/ragflow](https://hub.docker.com/r/infiniflow/ragflow) |
| bitnami/spark | 3.5 | [hub.docker.com/r/bitnami/spark](https://hub.docker.com/r/bitnami/spark) |
