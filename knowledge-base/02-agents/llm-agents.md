---
title: LLM agents
tags: [agents, llm]
status: stable
---

# LLM agents

`LlmAgent` (alias `Agent`) is the reasoning unit: it sends the conversation to a
model, lets the model call tools or transfer to another agent, and produces text.

## Key parameters

| Parameter | Purpose |
| --- | --- |
| `name` | Unique identifier in the agent tree. Shows up as `event.author`. Use `snake_case`. |
| `model` | Model id string (`"gemini-2.5-flash"`) or a `BaseLlm` instance (`LiteLlm(...)`). |
| `description` | What this agent does. **Other agents read this** to decide whether to delegate. |
| `instruction` | System prompt. String with `{state_key}` placeholders, or a function of `ReadonlyContext`. |
| `tools` | Functions, `BaseTool`s or toolsets. |
| `sub_agents` | Agents this one can transfer to. |
| `output_key` | Saves the final text response into `state[output_key]`. |
| `output_schema` | Pydantic model; forces structured JSON output. Disables tool use on that agent. |
| `input_schema` | Pydantic model the input must match when the agent is used as a tool. |
| `include_contents` | `"default"` sends history; `"none"` sends only the current input, useful for stateless steps. |
| `generate_content_config` | Temperature, max tokens, safety settings (`google.genai.types.GenerateContentConfig`). |
| `planner` | `BuiltInPlanner` (model-native thinking) or `PlanReActPlanner` (prompted plan/act/reason). |
| `code_executor` | Lets the agent run code it writes. |

## Instructions and state templating

```python
from google.adk.agents import LlmAgent

writer = LlmAgent(
    name="writer",
    model="gemini-2.5-flash",
    instruction=(
        "You write short summaries for {user:display_name?}.\n"
        "Topic: {topic}\n"
        "Use the research notes below.\n{research_notes}"
    ),
    output_key="summary",
)
```

- `{key}` is replaced with `state["key"]`; missing keys raise an error.
- `{key?}` is optional and becomes empty if missing.
- `{artifact.filename}` inserts a text artifact.
- For full control pass a function: `instruction=lambda ctx: f"... {ctx.state['x']}"`.

## Structured output

```python
from pydantic import BaseModel, Field

class Triage(BaseModel):
    category: str = Field(description="billing, technical or other")
    urgency: int = Field(ge=1, le=5)

triage_agent = LlmAgent(
    name="triage",
    model="gemini-2.5-flash",
    instruction="Classify the user's request.",
    output_schema=Triage,
    output_key="triage",
)
```

Because `output_schema` disables tools, pair it with a tool-using agent in a
[sequential pipeline](workflow-agents.md) when you need both.

## Planners and visible reasoning

- `BuiltInPlanner(thinking_config=types.ThinkingConfig(include_thoughts=True))`
  asks thinking-capable models to return thought parts. These appear in events
  as parts with `thought=True`.
- `PlanReActPlanner()` prompts any model to write `/*PLANNING*/`, `/*ACTION*/`,
  `/*REASONING*/` sections, which works for providers without native thinking.

Both are useful for [reasoning paths](../07-observability-transparency/reasoning-paths.md),
but treat model-reported reasoning as a hint, not ground truth. The tool call
sequence is the reliable record.

## Pitfalls

- A vague `description` causes wrong delegation in multi-agent setups.
- Very long instructions dilute tool-use accuracy; move reference material to
  state or artifacts and template it in.
- `output_key` stores only the final text, not intermediate tool results.

## See also

- [Tools](../03-tools/README.md)
- [Prompt engineering](../11-patterns-and-practices/prompt-engineering.md)
