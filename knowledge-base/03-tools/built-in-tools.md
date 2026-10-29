---
title: Built-in tools
tags: [tools, gemini]
status: draft
---

# Built-in tools

ADK ships ready-made tools. Several rely on **Gemini-native** features, so they
do not work when the agent runs on Claude or OpenAI models. Keep that in mind
for a model-agnostic design.

| Tool | Import | Notes |
| --- | --- | --- |
| Google Search | `from google.adk.tools import google_search` | Gemini grounding. Gemini 2.x models only. |
| Code execution | `from google.adk.code_executors import BuiltInCodeExecutor` | Set as `code_executor=` on the agent. Gemini only. |
| Vertex AI Search | `from google.adk.tools import VertexAiSearchTool` | Grounding on your own data stores. |
| URL context, BigQuery, Spanner and others | `google.adk.tools.*` | See docs for the current list. |
| `load_memory` / `preload_memory` | `from google.adk.tools import load_memory` | Query the memory service. Provider-neutral. |
| `load_artifacts` | `from google.adk.tools import load_artifacts` | Let the model pull artifacts into context. Provider-neutral. |

> Version note: historically a Gemini built-in tool such as `google_search`
> could not be combined with other tools in the same agent. Work around this by
> putting it in a dedicated agent and exposing that agent with
> [AgentTool](agent-as-tool.md). Check the current docs; this limit has been
> relaxed for some tools.

## Model-agnostic guidance

- Wrap provider-specific tools behind a small dedicated agent so the rest of the
  system does not depend on them.
- For search on any provider, use a function tool calling a search API, or an
  MCP search server.
- Code execution on non-Gemini models: use `UnsafeLocalCodeExecutor` only in
  trusted local experiments, or a sandboxed executor such as a container.
