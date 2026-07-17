# Subtask: SyntraFlow Layout-Aware Chunking

**Parent Link:** `base/syntraflow_rag.md`

## Actionable Steps
- [x] Split documents by logical layout boundaries (headers, paragraphs, sections) instead of arbitrary tokens.
- [x] Maintain parent-child mappings for headers to conserve hierarchy context.
- [x] Set defaults: `CHUNK_MAX_TOKENS=512`, `CHUNK_OVERLAP=50`, min size 50 tokens.

## Definition of Done
- Text chunks are parsed with parent header context preserved.
