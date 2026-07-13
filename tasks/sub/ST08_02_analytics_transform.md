# ST08_02: Implement Analytics Transformations

## Parent Link
`base/BT08_analytics_pipeline.md`

## Actionable Steps
- [ ] Step 1: Compute total_interactions per user via groupBy + count
- [ ] Step 2: Extract top_topics using NLP-based topic extraction (or keyword frequency)
- [ ] Step 3: Calculate avg_session_minutes from session duration data
- [ ] Step 4: Handle null values and edge cases (new users, zero interactions)

## Dependencies
- `ST08_01_spark_jdbc.md` — Raw data loaded into Spark DataFrame

## Definition of Done
Transformations produce accurate analytics DataFrame matching the user_analytics schema.
