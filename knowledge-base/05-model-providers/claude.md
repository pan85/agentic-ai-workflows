---
title: Claude
tags: [models, claude, anthropic]
status: draft
---

# Claude

Anthropic's Claude models are strong at tool use, long instructions and coding.
Two ways to use them in ADK:

## 1. Anthropic API via LiteLLM

```python
from google.adk.models.lite_llm import LiteLlm

model = LiteLlm(model="anthropic/claude-sonnet-5-5")   # ANTHROPIC_API_KEY in env
```

## 2. Claude on Vertex AI

ADK includes a `Claude` model class for Vertex AI's Model Garden. Register it,
then use the Vertex model id string.

```python
from google.adk.models.anthropic_llm import Claude
from google.adk.models.registry import LLMRegistry

LLMRegistry.register(Claude)
agent = LlmAgent(name="analyst", model="claude-sonnet-5-5@<version>", ...)
```

> Version note: requires `GOOGLE_GENAI_USE_VERTEXAI=TRUE`, the Anthropic SDK
> with Vertex extras, and the model enabled in your project. Check the Vertex
> AI model id format before use.

## Model tiers

| Tier | Use |
| --- | --- |
| Opus | Hardest reasoning, planning, complex multi-step tool use |
| Sonnet | Default workhorse: tool use, coding, analysis |
| Haiku | Fast, cheap: routing, classification, extraction |

Check Anthropic's model list for current ids.

## Notes

- Gemini built-in tools are not available; use function or MCP tools.
- Extended thinking is a provider-specific parameter; pass it through LiteLLM
  if needed, and capture thinking blocks separately in the run log.
- Claude follows system instructions closely, so concise, explicit
  instructions with clear tool-use rules work well.
