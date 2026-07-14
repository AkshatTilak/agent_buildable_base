# Subtask: Database Security

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [ ] Verify all queries use parameterized queries — no string-formatted SQL.
- [ ] PostgreSQL: configure dedicated user with minimal privileges (not superuser).
- [ ] Neo4j: verify read-only mode enforcement in `common/clients/neo4j.py`.
- [ ] Qdrant: use `QDRANT_API_KEY` when exposed to network.

## Dependencies
- Database clients in `common/clients/`. Docker-compose DB configurations.

## Definition of Done
- No raw string SQL/Cypher anywhere. DB users have minimal privileges. Qdrant secured when networked.
