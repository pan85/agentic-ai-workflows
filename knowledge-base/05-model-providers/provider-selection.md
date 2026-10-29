---
title: Provider selection
tags: [models, providers, cost]
status: draft
---

# Provider selection

Pick a model **per agent role**, not one model for the whole system.

## Role guide

| Role | Needs | Good starting point |
| --- | --- | --- |
| Router / triage | Speed, cheap, good classification | Gemini Flash, Claude Haiku |
| General tool user | Reliable function calling | Gemini Flash, Claude Sonnet, GPT general model |
| Deep reasoning / planning | Accuracy over latency | Gemini Pro, Claude Opus, OpenAI reasoning model |
| Coding | Code generation and editing | Claude Sonnet/Opus, Codex-family model |
| Search-grounded answers | Web grounding | Gemini with `google_search` (wrapped as AgentTool) |
| Bulk extraction | Cost per token | Flash-Lite, Haiku |

## Capability matrix (verify per model)

| Capability | Gemini | Claude | OpenAI |
| --- | --- | --- | --- |
| Function calling | ✓ | ✓ | ✓ |
| Structured output | ✓ | ✓ (via tool/JSON mode) | ✓ |
| Native web search tool in ADK | ✓ | ✗ | ✗ |
| Native code execution in ADK | ✓ | ✗ | ✗ |
| Visible thinking | ✓ (`include_thoughts`) | ✓ (extended thinking) | Partial (summaries) |
| Live audio/video | ✓ | ✗ | ✗ in ADK |

## Selection process

1. Write the eval set first ([evaluation](../08-evaluation-testing/README.md)).
2. Run it against two or three candidate models.
3. Compare: eval scores, tool trajectory accuracy, latency (p50/p95), cost per
   run, and quality of visible reasoning.
4. Record the choice and its numbers in the agent's README or an ADR.

## Fallbacks and resilience

- LiteLLM supports retries and fallbacks; configure them in the factory, not in
  each agent.
- Log which model actually served each call so fallbacks stay visible.
- Rate limits differ by provider; set timeouts and backoff on every model.
