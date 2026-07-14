# BT05: RAGFlow Integration

## Objective
Integrate the RAGFlow engine for superior document understanding with OCR, layout recognition, and semantic chunking, exposed as a LangChain tool.

## Business/System Value
RAGFlow provides document parsing capabilities (DeepDoc OCR, table extraction) that far exceed basic text splitters, critical for enterprise document workflows.

## Subtask Registry
- [x] `sub/ST05_01_ragflow_client.md` — Implement RAGFlow SDK client wrapper
- [x] `sub/ST05_02_ragflow_tool.md` — Implement LangChain @tool for RAGFlow retrieval
- [ ] `sub/ST05_03_ragflow_docker.md` — Validate RAGFlow Docker deployment and API connectivity

## Complexity Rating
**Medium** — SDK integration with careful error handling and Docker networking.

## Source Code Location
`../../echomind-core/app/rag/`, `../../echomind-core/app/agent/tools/ragflow_retrieval.py`

## Partial Completion Note
ST05_01 and ST05_02 were completed as part of BT04 (providing the tool set for the agent).
ST05_03 (Docker validation) remains — requires manual testing with running containers.
