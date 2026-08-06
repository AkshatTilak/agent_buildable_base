# AI & Hybrid Search Logic Reference

## 1. 3-Way Hybrid Search Reciprocal Rank Fusion (RRF)
The `search-service` merges 3 distinct search streams:

1. **PostGIS Spatial Search**: Spatial bounding box / radius distance ranking (`ST_Distance`).
2. **Lexical Keyword Search (BM25)**: OpenSearch / Postgres BM25 full-text matching on facility metadata.
3. **Semantic Vector Search (`pgvector`)**: Cosine similarity on `text-embedding-3-small` dense vectors.

### Fusion Formula
For each document $d$:
$$RRF(d) = \frac{1}{k + r_{\text{spatial}}(d)} + \frac{1}{k + r_{\text{lexical}}(d)} + \frac{1}{k + r_{\text{semantic}}(d)}$$
*Default constant $k = 60$.*

---

## 2. Profile Auto-Enrichment Agent Pipeline
1. **Trigger**: Facility claimed or directory link submitted.
2. **Crawl**: Scrape public web pages.
3. **Extraction**: LLM prompt parses structured JSON schema (amenities, staff ratios, pricing).
4. **Validation**: Check output confidence score against threshold $0.85$.
   - Score $\ge 0.85$: Staged for instant facility admin confirmation.
   - Score $< 0.85$: Routed to `web-admin` human-in-the-loop review queue.

---

## 3. Human-in-the-Loop Review Queue Rule
Low-confidence AI extractions MUST NEVER auto-overwrite active production facility records. Low-confidence outputs are written to `staged_ai_outputs` with diff highlights for human approval.
