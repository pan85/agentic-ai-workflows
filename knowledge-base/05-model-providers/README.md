# 05 · Model providers

How the framework stays model-agnostic, and what to know about each provider.

| Note | Summary |
| --- | --- |
| [model-abstraction.md](model-abstraction.md) | `BaseLlm`, `LiteLlm`, the registry, and our config-driven model factory |
| [gemini.md](gemini.md) | Gemini via AI Studio or Vertex AI; native features |
| [claude.md](claude.md) | Claude via Anthropic API (LiteLLM) or Vertex AI |
| [openai.md](openai.md) | OpenAI models (including Codex-family models) via LiteLLM |
| [provider-selection.md](provider-selection.md) | Choosing a model per agent, capability matrix, fallbacks and cost |

Decision record: [ADR 0002 · Model-agnostic via LiteLLM](../decisions/0002-model-agnostic-via-litellm.md).
