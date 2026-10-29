# 0001 · Adopt Google ADK as the agent framework

- **Status**: Accepted
- **Date**: 2026-10-29

## Context

We want to learn, build, version and deploy multi-domain agents, with execution
transparency as the core requirement. We need multi-agent orchestration, tool
integration (including MCP), session persistence, evaluation and deployment
paths, without locking into one model provider.

## Decision

Build on Google Agent Development Kit (Python, `google-adk`).

## Alternatives considered

- **LangGraph**: strong graph orchestration and checkpoints, but more assembly
  needed for eval and deployment, and a different mental model to learn.
- **Provider SDK agent frameworks** (Claude Agent SDK, OpenAI Agents SDK): good
  for one provider; weaker fit for a provider-neutral core.
- **Custom framework**: maximum control, but we would rebuild sessions, events,
  tracing and eval.

## Consequences

- Events, sessions and OpenTelemetry tracing come built in, giving a base for
  transparency.
- Gemini-native features are easiest; other providers go through LiteLLM (see 0002).
- ADK is evolving quickly; notes mark version-sensitive details and we pin the
  ADK version.
