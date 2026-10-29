---
title: Runtime architecture
tags: [adk, runtime, events]
status: stable
---

# Runtime architecture

How ADK executes one user turn. Understanding this is the basis for execution
transparency: every observable artefact (events, state deltas, spans) comes from
this loop.

## Components

| Component | Responsibility |
| --- | --- |
| `Runner` | Entry point. Takes `user_id`, `session_id` and a new message, drives the agent, yields events. |
| `SessionService` | Loads and saves sessions; appends events and applies state deltas. |
| `ArtifactService` | Stores versioned artifacts. Optional. |
| `MemoryService` | Long-term searchable memory. Optional. |
| `InvocationContext` | Per-invocation bag passed to agents: session, services, `invocation_id`, the running agent, `end_invocation` flag. |
| Agents | Implement `_run_async_impl(ctx)` and yield `Event`s. |

## The event loop

1. The caller sends a message to `Runner.run_async(...)`.
2. The Runner loads the session and appends the user message as an event.
3. The Runner calls `root_agent.run_async(ctx)`.
4. The agent does work and **yields** an event (for example a model response
   containing a function call).
5. The Runner receives the event, commits its `actions.state_delta` and
   `artifact_delta` through the services, appends it to the session and
   forwards it to the caller.
6. Only then does the agent's generator resume. This means state written in
   step 4 is guaranteed committed when the agent continues.
7. Repeat until the agent finishes; the last model text event is the final
   response (`event.is_final_response()`).

For an `LlmAgent` a single turn usually looks like:

```text
user event
 └─ model event: function_call(get_weather, {city: "Sofia"})
     └─ tool event: function_response({forecast: ...})
         └─ model event: text "It is sunny in Sofia."  ← final response
```

## Driving the Runner from code

```python
import asyncio
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
from google.genai import types

from my_agent.agent import root_agent

async def main() -> None:
    sessions = InMemorySessionService()
    runner = Runner(agent=root_agent, app_name="demo", session_service=sessions)
    session = await sessions.create_session(app_name="demo", user_id="u1")

    message = types.Content(role="user", parts=[types.Part(text="Weather in Sofia?")])
    async for event in runner.run_async(
        user_id="u1", session_id=session.id, new_message=message
    ):
        for call in event.get_function_calls():
            print(f"[{event.author}] tool call {call.name}({call.args})")
        for resp in event.get_function_responses():
            print(f"[{event.author}] tool result {resp.name} -> {resp.response}")
        if event.is_final_response() and event.content and event.content.parts:
            print(f"[{event.author}] final: {event.content.parts[0].text}")

asyncio.run(main())
```

`InMemoryRunner(agent=..., app_name=...)` is a shortcut that wires in-memory
session, artifact and memory services.

> Version note: `create_session` and the other session service methods are
> `async` in ADK 1.x. Older 0.x examples call them synchronously.

## Streaming

- `run_async` yields partial events when streaming is enabled
  (`RunConfig(streaming_mode=StreamingMode.SSE)`); partial events have
  `event.partial == True` and are not persisted.
- `run_live` supports bidirectional audio/video streaming with Gemini Live models.

## Transparency notes

- The session's event list is a complete, ordered log of the invocation; group
  by `invocation_id` to isolate one turn.
- The Runner is the single choke point for every event, so it is where
  [plugins](../06-callbacks-and-plugins/plugins.md) attach to capture a run log.

## See also

- [Event stream](../07-observability-transparency/event-stream.md)
- [Sessions](../04-sessions-state-memory/sessions.md)
