# Subtask: Fix Backend Telemetry Hardcoded Values

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [ ] `gateway/api/telemetry.py:41` — Replace hardcoded `vram_usage_mb: 4096` with actual GPU memory query:
  - Try `torch.cuda.memory_allocated()` / `torch.cuda.get_device_properties()`.
  - Fallback: Try parsing `nvidia-smi --query-gpu=memory.used,memory.total --format=csv,noheader,nounits`.
  - Final fallback: Return `0` for both with a `gpu_available: false` flag.
- [ ] `gateway/api/telemetry.py:42` — Replace hardcoded `vram_total_mb: 16384` with actual total.
- [ ] `gateway/api/telemetry.py:43` — Replace hardcoded `active_agents: 2` with actual count from:
  - Query `AgentDefinition` table count, OR
  - Read from `AgentRuntimeManager` cache size.
- [ ] Add `disk_usage_percent` metric to telemetry response using `psutil.disk_usage('/')`.
- [ ] Add `active_jobs_count` to telemetry by querying `SyntraFlowJob` table for status='processing'.
- [ ] Add `gpu_available` boolean to telemetry response.

## Dependencies
- Postgres and database models must be functional.

## Definition of Done
- Telemetry endpoint returns actual system metrics, not hardcoded values.
- GPU metrics gracefully degrade to zeros when no GPU is available.
- `active_agents` reflects actual registered agent count.
