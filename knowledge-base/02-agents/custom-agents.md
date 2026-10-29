---
title: Custom agents
tags: [agents, orchestration, advanced]
status: stable
---

# Custom agents

When the built-in workflow agents are not enough (conditional branches,
dynamic agent selection, external system checks), subclass `BaseAgent` and
implement `_run_async_impl`.

## Example: route by triage result

```python
from typing import AsyncGenerator
from google.adk.agents import BaseAgent, LlmAgent
from google.adk.agents.invocation_context import InvocationContext
from google.adk.events import Event

class TriageRouter(BaseAgent):
    triage: LlmAgent
    billing: BaseAgent
    technical: BaseAgent

    def __init__(self, name: str, triage: LlmAgent, billing: BaseAgent, technical: BaseAgent):
        super().__init__(
            name=name, triage=triage, billing=billing, technical=technical,
            sub_agents=[triage, billing, technical],
        )

    async def _run_async_impl(self, ctx: InvocationContext) -> AsyncGenerator[Event, None]:
        async for event in self.triage.run_async(ctx):
            yield event

        category = (ctx.session.state.get("triage") or {}).get("category")
        target = self.billing if category == "billing" else self.technical

        async for event in target.run_async(ctx):
            yield event
```

## Rules

- Declare sub-agents both as fields and in `sub_agents` so ADK knows the tree
  (needed for transfer, dev UI and tracing).
- **Always re-yield** events from sub-agents. Swallowing them hides steps from
  the session and from observability.
- Read state through `ctx.session.state`. To write state, yield an event with
  `EventActions(state_delta={...})` rather than mutating the dict directly, so
  the change is recorded.
- Keep business decisions in code here and leave language tasks to LLM agents.

## Emitting your own events

```python
from google.adk.events import Event, EventActions
from google.genai import types

yield Event(
    author=self.name,
    invocation_id=ctx.invocation_id,
    content=types.Content(parts=[types.Part(text=f"Routing to {target.name}")]),
    actions=EventActions(state_delta={"route": target.name}),
)
```

Routing decisions recorded like this become explicit steps in the
[reasoning path](../07-observability-transparency/reasoning-paths.md).
