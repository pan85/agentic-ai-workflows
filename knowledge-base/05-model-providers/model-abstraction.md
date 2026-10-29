---
title: Model abstraction
tags: [models, providers, architecture]
status: draft
---

# Model abstraction

ADK talks to models through `BaseLlm`. An agent's `model` can be:

- a **string**, resolved by `LLMRegistry` (Gemini ids such as `"gemini-2.5-flash"`
  resolve to the native `Gemini` class), or
- a **`BaseLlm` instance**, such as `LiteLlm(model="anthropic/...")`.

Everything above the model (agents, tools, events, state) stays the same.

## LiteLlm

```python
from google.adk.agents import LlmAgent
from google.adk.models.lite_llm import LiteLlm

agent = LlmAgent(
    name="assistant",
    model=LiteLlm(model="anthropic/claude-sonnet-5-5"),
    instruction="You are a helpful assistant.",
)
```

`pip install litellm`. The `provider/model` string follows
[LiteLLM's naming](https://docs.litellm.ai/docs/providers); credentials come
from the usual environment variables (`ANTHROPIC_API_KEY`, `OPENAI_API_KEY`,
...). LiteLLM can also point at a LiteLLM proxy or local models (Ollama, vLLM).

## Project rule: never hard-code models in agents

Agents ask a factory for a model by **role**, and configuration maps roles to
concrete models. Switching provider is a config change, not a code change.

```python
# core/models/factory.py (planned)
import os
from google.adk.models.base_llm import BaseLlm
from google.adk.models.lite_llm import LiteLlm

# role -> model spec; override per environment with MODEL_<ROLE>
DEFAULTS = {
    "router": "gemini-2.5-flash",
    "reasoning": "anthropic/claude-sonnet-5-5",
    "coding": "openai/gpt-5-codex",
    "cheap": "gemini-2.5-flash-lite",
}

def model_for(role: str) -> str | BaseLlm:
    spec = os.getenv(f"MODEL_{role.upper()}", DEFAULTS[role])
    if spec.startswith("gemini"):
        return spec                 # native Gemini path keeps Gemini-only features
    return LiteLlm(model=spec)
```

```python
from core.models.factory import model_for

triage = LlmAgent(name="triage", model=model_for("router"), ...)
```

The model ids above are examples. Keep the real defaults in one config file and
check each provider's current model list.

## Portability checklist

- Avoid Gemini-only tools (`google_search`, built-in code execution) in agents
  that must run on any provider, or isolate them behind an
  [AgentTool](../03-tools/agent-as-tool.md).
- Keep tool schemas simple; providers differ in how they handle nested or
  optional parameters.
- `output_schema` support and strictness differ by provider. Validate outputs.
- Thinking/reasoning output is provider-specific. Use `PlanReActPlanner` if you
  need a uniform, visible plan across providers.
- Run the same eval set against each provider before switching
  (see [evaluation](../08-evaluation-testing/adk-evaluation.md)).

## Transparency notes

Record the resolved model id on every model call (the `call_llm` span carries
it; our run-log plugin also writes it) so that results can be compared across
providers.
