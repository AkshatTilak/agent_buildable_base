# Subtask: Network Security

**Parent Link:** `base/security_hardening.md`

## Actionable Steps
- [ ] Services within docker-compose communicate via internal Docker network only.
- [ ] Only gateway and admin tools expose ports to the host.
- [ ] Inference server NOT exposed externally — only accessible via gateway.
- [ ] All outbound API calls (Gemini, OpenRouter, Groq) use HTTPS.
- [ ] Set strict timeouts: LLM 60s, web search 2s, inference internal 120s.
- [ ] Handle provider outages via fallback chains.

## Dependencies
- docker-compose network configuration. HTTP client configurations.

## Definition of Done
- Internal services not reachable from host. HTTPS enforced. Timeouts configured.
