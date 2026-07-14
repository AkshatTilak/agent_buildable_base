# ST04_03: Implement User Analytics Tool

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [x] Step 1: Implement SQL query against user_analytics table
- [x] Step 2: Format analytics data (total_interactions, top_topics, avg_session_minutes)
- [x] Step 3: Register as LangChain @tool with descriptive docstring
- [x] Step 4: Gate tool access to premium/admin roles only

## Dependencies
- `ST02_01_init_db_sql.md` — user_analytics table exists ✅
- PySpark job (BT08) populates test data (BT08 is future work)

## Implementation Notes
- Uses ORM: `select(UserAnalytics).where(UserAnalytics.user_id == user_uuid)`
- RBAC gating done at orchestrator level (`get_agent_tools()`), not inside tool
- Tool returns descriptive "no data yet" message if PySpark hasn't populated the row
- UUID validation before DB query to prevent injection

## Definition of Done
Tool returns formatted user analytics, properly gated by RBAC roles. ✅

## Completion
**Status: [x] COMPLETE** — Implemented 2026-07-14.
`../../echomind-core/app/agent/tools/user_analytics.py`
