---
title: ADK overview
tags: [adk, fundamentals]
status: stable
---

# ADK overview

Google Agent Development Kit (ADK) is an open-source, code-first framework for
building, evaluating and deploying AI agents. It is optimised for Gemini and
Google Cloud but is model-agnostic and deployment-agnostic, which is why this
project builds on it.

- Python package: `pip install google-adk`
- Docs: <https://google.github.io/adk-docs/>
- Source: <https://github.com/google/adk-python> (Java and Go SDKs also exist)

## Building blocks

| Block | Role | Note |
| --- | --- | --- |
| Agents | Units of reasoning or orchestration | [02-agents](../02-agents/README.md) |
| Tools | Capabilities agents can call | [03-tools](../03-tools/README.md) |
| Runner | Executes an agent for a user turn | [runtime architecture](runtime-architecture.md) |
| Sessions and state | Conversation history and scratchpad | [04-sessions-state-memory](../04-sessions-state-memory/README.md) |
| Memory and artifacts | Long-term knowledge and files | [memory](../04-sessions-state-memory/memory.md), [artifacts](../04-sessions-state-memory/artifacts.md) |
| Models | LLM backends behind a common interface | [05-model-providers](../05-model-providers/README.md) |
| Callbacks and plugins | Lifecycle hooks | [06-callbacks-and-plugins](../06-callbacks-and-plugins/README.md) |
| Events | The record of everything that happened | [event stream](../07-observability-transparency/event-stream.md) |
| Evaluation | Test agent behaviour and trajectories | [08-evaluation-testing](../08-evaluation-testing/README.md) |
| Deployment | Cloud Run, Agent Engine, GKE, any container host | [09-deployment](../09-deployment/README.md) |

## Smallest useful agent

```python
from google.adk.agents import Agent

def get_weather(city: str) -> dict:
    """Return the current weather for a city."""
    return {"status": "success", "city": city, "forecast": "sunny, 22°C"}

root_agent = Agent(
    name="weather_agent",
    model="gemini-2.5-flash",
    description="Answers questions about the weather.",
    instruction="Use get_weather to answer weather questions. Be concise.",
    tools=[get_weather],
)
```

Run it with `adk web` (browser dev UI) or `adk run <agent_dir>` (terminal).

## Why ADK for this project

- **Transparency built in**: every step is an `Event` persisted on the session,
  and ADK emits OpenTelemetry spans for agent runs, model calls and tool calls.
  That is the raw material for our [observability layer](../07-observability-transparency/README.md).
- **Model-agnostic**: Gemini natively, Claude and OpenAI through `LiteLlm` or
  provider wrappers. See [ADR 0002](../decisions/0002-model-agnostic-via-litellm.md).
- **Composable**: LLM agents and deterministic workflow agents mix freely.
- **Eval and deploy tooling** in the same CLI.

See [ADR 0001](../decisions/0001-adopt-google-adk.md) for the decision record.

## Mental model

```text
User message
   │
   ▼
Runner ──loads──▶ Session (events + state)
   │
   ▼
root_agent.run_async(ctx) ──yields──▶ Event, Event, Event ...
   │                                   │
   │                         Runner applies state/artifact deltas,
   │                         appends event to session, streams to caller
   ▼
Final response event
```
