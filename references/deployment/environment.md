# Environment & Deployment Reference

## Local Development Prerequisites
- **Python**: 3.12+
- **Package Manager**: `uv` (`pip install uv`)
- **Container Runtime**: Docker Desktop / Podman

---

## Persistent Local Storage & Infrastructure Volumes
Local Docker services use persistent relative host path mounts inside the repository directory:

| Service | Container Path | Local Host Storage Path |
| --- | --- | --- |
| **PostgreSQL + PostGIS** | `/var/lib/postgresql/data` | `./infra/data/postgres` |
| **Redis** | `/data` | `./infra/data/redis` |
| **OpenSearch** | `/usr/share/opensearch/data` | `./infra/data/opensearch` |

*Note: `./infra/data/` is git-ignored while keeping `.gitkeep` placeholders.*

---

## Global Configuration System
Environment variables are defined in `.env` (derived from `.env.example`).
Services load configuration using the centralized package:

```python
from py_common.config import settings

# Access settings anywhere
db_url = settings.DATABASE_URL
gemini_key = settings.GEMINI_API_KEY
```

---

## Infrastructure Port Mappings

| Service | Internal Port | Host Port | Protocol |
| --- | --- | --- | --- |
| **PostgreSQL + PostGIS** | 5432 | 5432 | TCP |
| **Redis** | 6379 | 6379 | TCP |
| **OpenSearch** | 9200 | 9200 | HTTP |
| **auth-service** | 8000 | 8000 | HTTP |
| **taxonomy-service** | 8001 | 8001 | HTTP |
| **ingestion-service** | 8002 | 8002 | HTTP |
| **search-service** | 8003 | 8003 | HTTP |
| **ai-agent-service** | 8004 | 8004 | HTTP |
| **leads-service** | 8005 | 8005 | HTTP |
| **billing-service** | 8006 | 8006 | HTTP |
| **web-family** | 3000 | 3000 | HTTP |
| **web-facility** | 3001 | 3001 | HTTP |
| **web-admin** | 3002 | 3002 | HTTP |

---

## Quickstart Commands
```bash
# Copy example env if not present
cp .env.example .env

# Sync monorepo python dependencies
uv sync

# Spin up local database and search containers with persistent local volumes
docker compose -f infra/docker-compose.yml up -d

# Run database migrations
uv run alembic upgrade head
```
