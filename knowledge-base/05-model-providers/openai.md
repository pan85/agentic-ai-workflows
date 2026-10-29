---
title: OpenAI (GPT and Codex)
tags: [models, openai, codex]
status: draft
---

# OpenAI (GPT and Codex)

OpenAI models are used through LiteLLM.

```python
from google.adk.models.lite_llm import LiteLlm

general = LiteLlm(model="openai/gpt-5")          # OPENAI_API_KEY in env
coder = LiteLlm(model="openai/gpt-5-codex")      # example Codex-family id
```

Model ids are examples. Check OpenAI's current model list and LiteLLM's
OpenAI provider page; some newer models are only offered through the Responses
API, which LiteLLM addresses with a different prefix.

## Codex: two different things

- **Codex models**: coding-tuned OpenAI models. In this framework they are just
  another model behind `LiteLlm`, typically assigned to the `coding` role in the
  [model factory](model-abstraction.md).
- **Codex CLI / agent**: OpenAI's standalone coding agent. It is a separate
  product, not an ADK model. It can be used as a development tool on this repo,
  or wrapped as an external tool (for example via MCP or a subprocess tool) if
  an ADK agent needs to delegate a coding task to it.

## Notes

- Azure OpenAI works the same way with the `azure/` prefix and Azure env vars.
- Strict structured outputs and parallel tool calls are supported by most
  recent models; validate behaviour with an eval set before relying on it.
- Reasoning models may not expose their reasoning text; rely on the tool-call
  trajectory for transparency.
