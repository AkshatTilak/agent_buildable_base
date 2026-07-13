# Docker Setup Guide — EchoMind Core

## Quick Start

```bash
# 1. Clone and navigate
cd echomind-core

# 2. Create environment file
cp .env.example .env
# Edit .env with your API keys (GOOGLE_API_KEY, etc.)

# 3. Start all services
docker compose up -d

# 4. Verify services
docker compose ps

# 5. View logs
docker compose logs -f app
```

## Service URLs

| Service | URL | Purpose |
|---|---|---|
| FastAPI App | http://localhost:8000/docs | API documentation (Swagger) |
| Langfuse | http://localhost:3000 | LLM observability dashboard |
| Langflow | http://localhost:7860 | Visual agent builder |
| RAGFlow | http://localhost:9380 | Document RAG engine |
| Spark Master | http://localhost:8080 | Spark cluster dashboard |

## Useful Commands

```bash
# Rebuild app after code changes
docker compose up -d --build app

# Stop all services
docker compose down

# Stop and remove all data (clean slate)
docker compose down -v

# View PostgreSQL logs
docker compose logs postgres

# Connect to PostgreSQL CLI
docker compose exec postgres psql -U echomind echomind_db

# Connect to Redis CLI
docker compose exec redis redis-cli

# Kafka: list topics
docker compose exec kafka kafka-topics.sh --bootstrap-server localhost:9092 --list
```

## Local Development (Without Docker)

```bash
# Install Poetry
pip install poetry

# Install dependencies
poetry install

# Run FastAPI locally
poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

## Troubleshooting

- **PostgreSQL won't start**: Check if port 5432 is already in use
- **Kafka health check failing**: KRaft mode needs ~30s to initialize
- **RAGFlow slow to start**: First start downloads models, may take 5-10 minutes
- **Langfuse 500 error**: Ensure NEXTAUTH_SECRET and SALT are set in .env
