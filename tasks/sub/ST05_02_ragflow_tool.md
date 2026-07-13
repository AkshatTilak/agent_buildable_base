# ST05_02: Implement RAGFlow Retrieval Tool

## Parent Link
`base/BT05_rag_integration.md`

## Actionable Steps
- [ ] Step 1: Create LangChain @tool wrapping the RAGFlow client retrieve method
- [ ] Step 2: Accept query and dataset_id parameters
- [ ] Step 3: Format retrieved chunks with relevance scores
- [ ] Step 4: Add descriptive docstring for LLM tool selection
- [ ] Step 5: Test tool invocation through the agent orchestrator

## Dependencies
- `ST05_01_ragflow_client.md` — RAGFlow client must be functional

## Definition of Done
Tool returns RAGFlow-parsed chunks when invoked by the agent, docstring guides LLM correctly.
