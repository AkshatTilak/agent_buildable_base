# BT08: PySpark Analytics Pipeline

## Objective
Implement the PySpark batch ETL pipeline that reads chat interaction data from PostgreSQL, computes per-user analytics, and writes results back to the `user_analytics` table.

## Business/System Value
Enables the LangChain agent to answer user questions about their own interaction patterns by providing pre-computed analytics as tool-accessible data.

## Subtask Registry
- [ ] `sub/ST08_01_spark_jdbc.md` — Implement partitioned JDBC read with lowerBound/upperBound/numPartitions
- [ ] `sub/ST08_02_analytics_transform.md` — Implement user analytics transformations (interactions, topics, sessions)
- [ ] `sub/ST08_03_analytics_writeback.md` — Implement JDBC write-back to user_analytics table

## Complexity Rating
**Medium-High** — Requires Spark cluster management and JDBC performance tuning.

## Source Code Location
`../../echomind-core/analytics/`
