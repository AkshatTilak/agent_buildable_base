# Agent Orchestration Flow — EchoMind Core

## Request Lifecycle

```mermaid
sequenceDiagram
    participant U as User
    participant FA as FastAPI
    participant RD as Redis
    participant AG as LangChain Agent
    participant LLM as Gemini LLM
    participant T as Tools
    participant LFU as Langfuse

    U->>FA: POST /api/v1/chat {query, session_id}
    FA->>RD: Validate JWT + Get Role
    RD-->>FA: {role_id, permissions}
    FA->>AG: run_agent(query, user_id, session_id, role)
    AG->>LFU: @observe() trace start
    AG->>RD: Get chat history (last 20 messages)
    RD-->>AG: [message_history]
    AG->>LLM: System prompt + history + query
    LLM-->>AG: Tool call request (e.g., vector_search)

    alt Tool Authorized
        AG->>T: Execute tool with RBAC check
        T-->>AG: Tool result
        AG->>LLM: Tool result → Generate response
    else Tool Unauthorized
        AG->>LLM: "User lacks permission for this tool"
    end

    LLM-->>AG: Final response
    AG->>RD: Save to chat history
    AG->>LFU: @observe() trace end
    AG-->>FA: Response text
    FA-->>U: {response, sources}
```

## Tool Selection Logic

The LLM determines which tool to invoke based on:
1. **Tool docstrings** — Each @tool function has a descriptive docstring
2. **System prompt** — Includes instructions on when to use each tool
3. **User role** — Only tools the user is authorized for are loaded

## Available Tools

| Tool | Function | Gate |
|---|---|---|
| `vector_similarity_search` | pgvector cosine search on user documents | All roles |
| `ragflow_retrieve` | RAGFlow deep document retrieval | All roles |
| `query_user_analytics` | PySpark-computed user analytics | premium, admin |
