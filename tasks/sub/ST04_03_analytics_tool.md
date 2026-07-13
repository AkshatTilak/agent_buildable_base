# ST04_03: Implement User Analytics Tool

## Parent Link
`base/BT04_agent_orchestration.md`

## Actionable Steps
- [ ] Step 1: Implement SQL query against user_analytics table
- [ ] Step 2: Format analytics data (total_interactions, top_topics, avg_session_minutes)
- [ ] Step 3: Register as LangChain @tool with descriptive docstring
- [ ] Step 4: Gate tool access to premium/admin roles only

## Dependencies
- `ST02_01_init_db_sql.md` — user_analytics table exists
- PySpark job should have populated test data

## Definition of Done
Tool returns formatted user analytics, properly gated by RBAC roles.
