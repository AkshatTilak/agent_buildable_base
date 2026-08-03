# Subtask 02_03: Proxy Structured JSON Error Handling

## Tasks
1. Update `gateway/api/proxy.py` to check `Accept` headers or path type.
2. When proxying API requests to Qdrant or Neo4j fails, return clean JSON error payloads (`{"error_code": "PROXY_SERVICE_UNAVAILABLE", "message": "...", "service": "qdrant"}`) instead of hardcoded HTML error strings.
3. Ensure iframe asset proxy routes handle unreachable states with clean minimal CSS cards without breaking parent application layouts.
