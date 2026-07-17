# Subtask: Network Security

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [x] Services within docker-compose communicate via internal Docker network only.
- [x] Only gateway and admin tools expose ports to the host.
- [x] Inference server NOT exposed externally — only accessible via gateway.
- [x] All outbound API calls (Gemini, OpenRouter, Groq) use HTTPS.
- [x] Set strict timeouts: LLM 60s, web search 2s, inference internal 120s.
- [x] Handle provider outages via fallback chains.

## Dependencies
- docker-compose network configuration. HTTP client configurations.

## Definition of Done
- Internal services not reachable from host. HTTPS enforced. Timeouts configured.
