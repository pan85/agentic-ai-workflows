---
title: Agent as tool
tags: [tools, agents, multi-agent]
status: stable
---

# Agent as tool

`AgentTool` wraps an agent so another agent can call it like a function. The
caller sends an input, the wrapped agent runs to completion in isolation, and
its final response (or `output_key` value) returns as the tool result.

```python
from google.adk.agents import LlmAgent
from google.adk.tools import google_search
from google.adk.tools.agent_tool import AgentTool

search_agent = LlmAgent(
    name="web_search",
    model="gemini-2.5-flash",
    description="Searches the web and returns a short factual summary with sources.",
    instruction="Search for the request and summarise the findings with links.",
    tools=[google_search],
)

analyst = LlmAgent(
    name="analyst",
    model="gemini-2.5-pro",
    instruction="Answer research questions. Use web_search for current facts.",
    tools=[AgentTool(agent=search_agent)],
)
```

## When to prefer it over sub-agents

- The parent must **combine** several helpers' results.
- The helper should not see the full conversation.
- You need a Gemini-only tool next to other tools (see [built-in tools](built-in-tools.md)).
- You want the helper to be swappable to a different model provider without
  affecting the parent.

## Options

- `skip_summarization=True`: return the helper's output verbatim.
- Give the wrapped agent an `input_schema` to validate what the parent sends.

## Transparency notes

The parent's events show one function call and one response; the helper's
inner steps are visible in the trace as a nested agent span. If you need the
helper's internal tool calls in the run log, capture them with a
[plugin](../06-callbacks-and-plugins/plugins.md), which sees every agent.
