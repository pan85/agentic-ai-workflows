---
title: Learning path
tags: [meta, learning]
status: stable
---

# Learning path

A staged route from zero to deployed, transparent multi-agent systems. Each
stage lists what to read and a small exercise to prove you understood it.

## Stage 1 · First agent

- Read: [ADK overview](../01-adk-fundamentals/overview.md),
  [project layout](../01-adk-fundamentals/project-layout.md),
  [LLM agents](../02-agents/llm-agents.md).
- Exercise: build a single agent with one function tool (for example a currency
  converter). Run it with `adk web` and inspect the Events tab.

## Stage 2 · How a run works

- Read: [runtime architecture](../01-adk-fundamentals/runtime-architecture.md),
  [event stream](../07-observability-transparency/event-stream.md).
- Exercise: drive the same agent from a Python script with `Runner.run_async`
  and print every event's author, function calls and final text.

## Stage 3 · Tools in depth

- Read: everything in [03-tools](../03-tools/README.md).
- Exercise: add a tool that writes to state through `ToolContext`, and one MCP
  tool (for example the filesystem MCP server).

## Stage 4 · State and memory

- Read: [sessions](../04-sessions-state-memory/sessions.md),
  [state](../04-sessions-state-memory/state.md),
  [memory](../04-sessions-state-memory/memory.md).
- Exercise: persist sessions with `DatabaseSessionService` (SQLite), restart the
  process and continue the conversation. Use `user:` state for a preference.

## Stage 5 · Swap the model

- Read: [model providers](../05-model-providers/README.md).
- Exercise: run the stage 1 agent on Gemini, Claude and an OpenAI model by
  changing only configuration. Compare tool-calling behaviour in the trace.

## Stage 6 · Multiple agents

- Read: [workflow agents](../02-agents/workflow-agents.md),
  [multi-agent systems](../02-agents/multi-agent-systems.md).
- Exercise: a coordinator that routes to two specialists, plus a
  `SequentialAgent` pipeline (draft, review, finalise) using `output_key`.

## Stage 7 · Make it transparent

- Read: all of [07-observability-transparency](../07-observability-transparency/README.md)
  and [callbacks](../06-callbacks-and-plugins/callbacks.md).
- Exercise: write a plugin that records every tool call (args, result,
  duration) to a JSON run log, and render the reasoning path for one run.

## Stage 8 · Prove it works

- Read: [evaluation](../08-evaluation-testing/README.md).
- Exercise: create an eval set with three cases and run `adk eval`. Add it to pytest.

## Stage 9 · Ship it

- Read: [deployment](../09-deployment/README.md).
- Exercise: containerise the agent and deploy to Cloud Run; tag the release
  following [versioning](../09-deployment/versioning.md).
