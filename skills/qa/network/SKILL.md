---
version: 1.0.0
updated: 2026-08-18
id: skill_qa_network
links:
  - ../../../references/deployment/optimization_checklist.md
  - ../../../workflows/execution/work_verification.md
  - ../../../workflows/quality/fallback_policy.md
---

# Skill: QA — Network

## Trigger Conditions
- Load this skill when auditing or testing networking.
- Load when verifying connectivity, ports, DNS, proxies, or firewalls.

## Steps
1. **Verify connectivity** between services (containers, hosts, external APIs).
2. **Verify ports and bindings** match the configuration.
3. **Verify DNS / hostnames** resolve correctly.
4. **Verify security**: no exposed secrets, correct TLS, appropriate firewall
   rules.
5. **Verify resilience**: retries and timeouts are configured with intent (no
   blanket fallbacks — see `../../../workflows/quality/fallback_policy.md`).

## Expected Outputs
- Network verified end-to-end.
- Connectivity, ports, DNS, and security confirmed.
- Resilience configured correctly.

## References
- `../../../references/deployment/optimization_checklist.md`
- `../../../workflows/quality/fallback_policy.md`
