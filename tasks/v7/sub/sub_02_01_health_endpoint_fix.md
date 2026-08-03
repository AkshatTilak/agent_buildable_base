# Subtask 02_01: Health Endpoint Accuracy & Degraded Status Calculation

## Tasks
1. Refactor `gateway/api/health.py` `health_check()` function.
2. Remove hardcoded `"status": "healthy"`. Calculate overall status based on service checks:
   - `healthy`: Database, Redis, and Qdrant are connected.
   - `degraded`: Non-critical services (Neo4j, Kafka, Inference) unreachable while DB/Redis/Qdrant are active.
   - `unhealthy`: Core Database or Redis unreachable.
3. Update status code mapping (return 200 for healthy/degraded, 503 for unhealthy).
4. Update frontend health polling widgets to handle `"degraded"` status.
