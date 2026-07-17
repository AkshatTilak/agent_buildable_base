# Base Task: SyntraFlow Ingestion & Hybrid RAG

## Objective
Implement multi-modal ingestion (documents, audio, video), layout-aware OCR/ASR pipeline, index population across Postgres, Qdrant, and Neo4j databases, and a FastMCP retrieval server.

## Business/System Value
Serves as the data input and retrieval gateway for the platform. Converts unstructured user documents and recordings into structured database rows, vector points, and Knowledge Graph nodes/edges.

## Subtask Registry
- [x] `sub/syntraflow_ocr_ingestion.md` — Layout-aware OCR parsing
- [x] `sub/syntraflow_chunking.md` — Parent-child context-aware chunking
- [x] `sub/syntraflow_media_demuxing.md` — Async video to audio extraction
- [x] `sub/syntraflow_asr_transcription.md` — STT transcription with event/emotion tagging
- [x] `sub/syntraflow_keyframe_sampling.md` — Video keyframe sampling and cloud LLM summarization
- [x] `sub/syntraflow_temporal_aligner.md` — Aligning transcripts and keyframe summaries chronologically
- [x] `sub/syntraflow_upload_limits.md` — Upload file validations (formats, sizes)
- [x] `sub/syntraflow_duplicate_detection.md` — SHA-256 hash checks on upload
- [x] `sub/syntraflow_job_tracker.md` — Status tracking table and Kafka execution queue
- [x] `sub/syntraflow_db_writing.md` — Batch writing and population to PostgreSQL, Qdrant, and Neo4j
- [x] `sub/syntraflow_mcp_server.md` — FastMCP SSE server with retrieval tools
- [x] `sub/syntraflow_deletion_cascade.md` — Cascade delete APIs

## Complexity Rating
**High**

## References
- `references/logic/syntraflow.md` — Ingestion specs.
- `references/structure/system_architecture.md` — DB setup specs.
