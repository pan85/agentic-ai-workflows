---
title: Multi-agent systems
tags: [agents, multi-agent, delegation]
status: stable
---

# Multi-agent systems

Complex applications are trees of agents. ADK gives three ways for agents to
work together.

## 1. Shared session state

Agents in the same invocation read and write the same `session.state`. This is
the main channel in [workflow agents](workflow-agents.md): one agent's
`output_key` is the next agent's `{placeholder}`.

## 2. LLM-driven delegation (transfer)

An `LlmAgent` with `sub_agents` can hand control to one of them. ADK adds a
`transfer_to_agent` function to the model's tools and lists the candidates using
their `name` and `description`.

```python
from google.adk.agents import LlmAgent

billing = LlmAgent(name="billing", model="gemini-2.5-flash",
                   description="Handles invoices, refunds and payment problems.",
                   instruction="Help with billing questions.")
support = LlmAgent(name="tech_support", model="gemini-2.5-flash",
                   description="Handles bugs, errors and how-to questions.",
                   instruction="Help with technical questions.")

coordinator = LlmAgent(
    name="help_desk",
    model="gemini-2.5-flash",
    description="Front door for customer requests.",
    instruction="Route the user to billing or tech_support. Do not answer yourself.",
    sub_agents=[billing, support],
)
```

- After transfer, the target agent handles following turns until it transfers back.
- Restrict movement with `disallow_transfer_to_parent=True` and
  `disallow_transfer_to_peers=True` on a sub-agent.
- An agent instance can have only **one parent**. Create a new instance to reuse
  the same configuration elsewhere.

## 3. Explicit invocation (agent as tool)

Wrap an agent in `AgentTool` so the parent calls it like a function and gets its
result back, staying in control. See [agent as tool](../03-tools/agent-as-tool.md).

| | Transfer | AgentTool |
| --- | --- | --- |
| Who continues the conversation | The sub-agent | The parent |
| Sub-agent sees full history | Yes | No, only the tool input |
| Good for | Routing to specialists | Calling a helper and using the result |

## Common topologies

- **Coordinator / dispatcher**: one router with specialist sub-agents.
- **Pipeline**: `SequentialAgent` of focused steps.
- **Fan-out / gather**: `ParallelAgent` followed by a synthesiser.
- **Generator / critic loop**: `LoopAgent` with a reviewer that escalates.
- **Hierarchical decomposition**: planner agent with `AgentTool` helpers.

See [design patterns](../11-patterns-and-practices/design-patterns.md) for when to use each.

## Transparency notes

- A transfer appears as an event whose `actions.transfer_to_agent` names the
  target, followed by events authored by that agent.
- Store the reason for routing in state (`state["route_reason"]`) or ask the
  coordinator to state it, so the reasoning path explains *why* control moved.
