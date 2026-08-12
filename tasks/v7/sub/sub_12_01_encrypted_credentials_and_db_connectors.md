# Subtask 12_01: Encrypted Credentials & Database Connector Infrastructure

## Objective
Implement a secure, hub-isolated external database credentials vault and connection pool manager in `common/` capable of connecting to PostgreSQL, MySQL, MongoDB, Redis (Snowflake/BigQuery optional) with at-rest credential encryption, read-only transaction safeguards, and parametrized query execution limits.

> **Path corrections:** Reuse the existing Fernet vault in `common/security/crypto.py` (do **not** create a new AES-256-GCM `vault.py`). The new credentials API is a **separate hub-scoped router** — the existing `gateway/api/credentials.py` (`/settings/credentials`) manages provider API keys and must not be conflated.

## Structural & Infrastructure Architecture

### 1. Database Model (`common/models/database.py`)
- **`ExternalCredential` ORM Model** (HubScopedMixin):
  - `id`: UUID (Primary Key)
  - `hub_id`: UUID (FK to Hub, hub-scoped isolation)
  - `name`: String (human-readable profile name, e.g. "Production Analytics DB")
  - `db_type`: Enum/String (`postgres`, `mysql`, `mongodb`, `redis`, `snowflake`, `bigquery`)
  - `host`: String
  - `port`: Integer
  - `database_name`: String
  - `username`: String
  - `encrypted_secret_payload`: Text (Fernet-encrypted JSON containing password, SSL keys, API tokens)
  - `is_read_only`: Boolean (default `True`)
  - `max_connections`: Integer (default 10)
  - `created_at`, `updated_at`
  - Register in `common/models/__init__.py` exports.

### 2. Encryption Service (`common/security/crypto.py`)
- Reuse existing `encrypt_secret()` / `decrypt_secret()` (Fernet). Add a small helper `encrypt_credential_payload(data: dict) -> str` and `decrypt_credential_payload(cipher_text: str) -> dict` that JSON-serialize then encrypt/decrypt, if not already present.

### 3. Connector Pool Manager (`common/clients/db_connectors/`)
- Abstract base class `BaseDatabaseConnector`:
  - `async def test_connection() -> bool`
  - `async def execute_query(query: str, params: dict, timeout_s: int = 30, max_rows: int = 1000) -> List[dict]`
  - `async def get_schema_metadata() -> dict`
- Drivers:
  - `PostgresConnector` (`asyncpg` / `sqlalchemy.ext.asyncio`)
  - `MySQLConnector` (`aiomysql`)
  - `MongoConnector` (`motor`)
  - `RedisConnector` (`redis-py` async)
  - (Optional) `SnowflakeConnector`, `BigQueryConnector`
- A `ConnectorPoolManager` that caches per-credential pools keyed by `credential_id`.

### 4. Safety & Guardrails
- **Read-Only Transaction Enforcement**: for query nodes, force `SET TRANSACTION READ ONLY` on SQL sessions.
- **Row Limit Guard**: cap result sets to `settings.MAX_EXTERNAL_DB_ROWS` (default 1,000).
- **Statement Timeout**: enforce strict query timeouts (`statement_timeout = 30000ms`).
- **Query sanitization**: reject multiple statements / DDL / DML when `is_read_only` is true.

## Tasks
1. `[x]` Create `ExternalCredential` ORM model in `common/models/database.py` and write Alembic migration in `migrations/versions/`.
2. `[x]` Add `encrypt_credential_payload` / `decrypt_credential_payload` helpers to `common/security/crypto.py` (reusing Fernet).
3. `[x]` Implement async connector pool manager and drivers in `common/clients/db_connectors/` for Postgres, MySQL, MongoDB, Redis.
4. `[x]` Build a **hub-scoped** Gateway REST API in `gateway/api/db_credentials.py` (`GET/POST/PUT/DELETE /hubs/{hub_id}/db-credentials` and `POST /hubs/{hub_id}/db-credentials/{id}/test`), guarded by `require_hub`. Never return `encrypted_secret_payload` in responses — return a masked/health summary instead.
5. `[x]` Write unit tests in `tests/test_db_connectors.py` verifying connection testing, query parameter binding, read-only transaction safeguards, and row limit truncation.
