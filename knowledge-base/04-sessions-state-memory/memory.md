---
title: Memory
tags: [memory, long-term]
status: draft
---

# Memory

Memory is long-term, searchable knowledge that outlives a session. State holds
structured facts you know you will need; memory holds a large body of past
interactions or documents you search when relevant.

## Services

| Service | How it works | Use for |
| --- | --- | --- |
| `InMemoryMemoryService` | Keyword matching in process memory | Prototyping and tests |
| `VertexAiMemoryBankService` | Managed, LLM-extracted memories in Agent Engine | Production on Google Cloud |
| `VertexAiRagMemoryService` | Vertex AI RAG corpus | Document-style recall |

> Version note: service names and availability vary across ADK versions.
> Check the memory page of the docs.

## Flow

```text
Session ends ──▶ memory_service.add_session_to_memory(session)
                          │
New session ──▶ agent calls load_memory("query") ──▶ relevant snippets
```

```python
from google.adk.agents import LlmAgent
from google.adk.memory import InMemoryMemoryService
from google.adk.runners import Runner
from google.adk.tools import load_memory

memory = InMemoryMemoryService()
assistant = LlmAgent(
    name="assistant",
    model="gemini-2.5-flash",
    instruction="Use load_memory when the user refers to earlier conversations.",
    tools=[load_memory],
)
runner = Runner(agent=assistant, app_name="demo",
                session_service=sessions, memory_service=memory)

# after a conversation worth remembering
completed = await sessions.get_session(app_name="demo", user_id="u1", session_id=sid)
await memory.add_session_to_memory(completed)
```

- `load_memory`: model decides when to search.
- `preload_memory`: always retrieves before each turn (simpler, costs more tokens).
- From your own tools: `await tool_context.search_memory(query)`.

## Practices

- Decide explicitly **what** gets remembered; do not dump every session.
- Respect privacy: memory is per `user_id`; offer deletion.
- Log memory hits in the run log so answers that rely on recalled facts are explainable.
