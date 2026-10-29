---
title: Workflow agents
tags: [agents, orchestration]
status: stable
---

# Workflow agents

Workflow agents orchestrate sub-agents **deterministically**. They do not call a
model themselves, so their control flow is predictable, cheap and easy to trace.

## SequentialAgent

Runs sub-agents one after another, sharing the same session state. Pass data
between steps with `output_key` and `{placeholders}`.

```python
from google.adk.agents import LlmAgent, SequentialAgent

researcher = LlmAgent(
    name="researcher", model="gemini-2.5-flash",
    instruction="Research {topic}. List key facts.", output_key="facts",
)
drafter = LlmAgent(
    name="drafter", model="gemini-2.5-flash",
    instruction="Write a 3-paragraph article from these facts:\n{facts}",
    output_key="draft",
)
editor = LlmAgent(
    name="editor", model="gemini-2.5-flash",
    instruction="Tighten this draft and fix errors:\n{draft}",
    output_key="article",
)

pipeline = SequentialAgent(name="article_pipeline", sub_agents=[researcher, drafter, editor])
```

## ParallelAgent

Runs sub-agents concurrently. Each sub-agent gets its own branch of the
conversation history but **shares state**, so give each one a distinct
`output_key` to avoid races. Typical use: fan out, then gather with a following
agent in a `SequentialAgent`.

```python
from google.adk.agents import ParallelAgent

fan_out = ParallelAgent(name="gather_sources", sub_agents=[web_agent, docs_agent, tickets_agent])
flow = SequentialAgent(name="research_flow", sub_agents=[fan_out, synthesiser])
```

## LoopAgent

Repeats its sub-agents until `max_iterations` is reached or a sub-agent
escalates.

```python
from google.adk.agents import LoopAgent
from google.adk.tools import ToolContext

def approve(tool_context: ToolContext) -> dict:
    """Call when the draft meets all review criteria."""
    tool_context.actions.escalate = True
    return {"approved": True}

reviewer = LlmAgent(
    name="reviewer", model="gemini-2.5-flash",
    instruction="Review {draft}. If it is good call approve, else write feedback.",
    tools=[approve], output_key="feedback",
)
refiner = LlmAgent(
    name="refiner", model="gemini-2.5-flash",
    instruction="Improve {draft} using {feedback?}.", output_key="draft",
)

refine_loop = LoopAgent(name="refine_loop", sub_agents=[reviewer, refiner], max_iterations=4)
```

Always set `max_iterations`; a model that never approves would otherwise loop forever.

## Transparency notes

- Each sub-agent's events carry its own `author`, so a sequential or loop run
  reads as a clear timeline in the event stream.
- Record the loop iteration in state (for example via a callback) if you need
  per-iteration metrics.

## See also

- [Custom agents](custom-agents.md) for branching logic
- [Design patterns](../11-patterns-and-practices/design-patterns.md)
