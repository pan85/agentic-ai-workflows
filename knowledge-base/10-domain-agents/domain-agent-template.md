---
title: Domain agent template
tags: [domains, template, how-to]
status: draft
---

# Domain agent template

## Recipe

1. Copy the skeleton below into `agents/<domain>_agent/`.
2. Write the agent's README: purpose, users, tools, state keys, model roles.
3. Write three to five eval cases for the main trajectories.
4. Implement tools in `tools.py` (structured results with `status`).
5. Write the instruction in `prompts.py` and give it a version.
6. Wire `agent.py` using `model_for(role)` from the model factory.
7. Run `adk web agents/`, check the event and trace views, then run evals.
8. Add the domain to the [catalog](domain-catalog.md).

## Skeleton

```text
agents/<domain>_agent/
├── __init__.py
├── agent.py
├── prompts.py
├── tools.py
├── README.md
├── CHANGELOG.md
└── evals/
    ├── main.test.json
    └── test_config.json
```

```python
# __init__.py
from . import agent

__version__ = "0.1.0"
```

```python
# prompts.py
INSTRUCTION_VERSION = "2026-10-29.1"

INSTRUCTION = """\
You are the <domain> assistant.

Goals:
- ...

Tool rules:
- Use <tool_a> when ...
- Never guess ids; ask the user.

When you route or decide, state the reason in one sentence.
"""
```

```python
# agent.py
from google.adk.agents import LlmAgent

from core.models.factory import model_for
from .prompts import INSTRUCTION
from .tools import tool_a, tool_b

root_agent = LlmAgent(
    name="<domain>_agent",
    model=model_for("reasoning"),
    description="Handles <domain> requests: ...",
    instruction=INSTRUCTION,
    tools=[tool_a, tool_b],
    output_key="<domain>:last_answer",
)
```

The run-log and policy plugins are attached in the shared Runner factory, so a
domain agent gets transparency without extra code.

## README sections for each agent

- Purpose and example requests
- Tools (name, what it calls, side effects)
- State keys read and written
- Model roles used and current mapping
- Eval coverage
- Known limitations
