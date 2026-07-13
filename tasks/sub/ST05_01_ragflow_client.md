# ST05_01: Implement RAGFlow Client

## Parent Link
`base/BT05_rag_integration.md`

## Actionable Steps
- [ ] Step 1: Initialize RAGFlow SDK client with API key and base URL from config
- [ ] Step 2: Implement `create_user_dataset()` with parser_config (chunk_token_num=512, layout_recognize=True)
- [ ] Step 3: Implement `upload_document()` with async parsing trigger
- [ ] Step 4: Implement `retrieve()` for semantic search within a dataset
- [ ] Step 5: Add error handling for API failures and timeouts

## Dependencies
- `ragflow-sdk` installed, RAGFlow container running

## Definition of Done
Client wrapper creates datasets, uploads/parses documents, and retrieves chunks successfully.
