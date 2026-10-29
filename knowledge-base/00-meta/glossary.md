---
title: Glossary
tags: [meta, reference]
status: stable
---

# Glossary

| Term | Meaning |
| --- | --- |
| **ADK** | Google Agent Development Kit, an open-source, code-first framework for building, evaluating and deploying agents. Python package `google-adk`. |
| **Agent** | A unit that receives context and produces events. Subclasses of `BaseAgent`. |
| **LlmAgent / Agent** | An agent driven by a language model that can reason, call tools and hand off to sub-agents. `Agent` is an alias of `LlmAgent`. |
| **Workflow agent** | A deterministic orchestrator (`SequentialAgent`, `ParallelAgent`, `LoopAgent`) that runs sub-agents in a fixed pattern without using an LLM to decide. |
| **Root agent** | The top of an agent tree; the one passed to the `Runner`. By convention exported as `root_agent`. |
| **Sub-agent** | An agent listed in another agent's `sub_agents`. Forms the agent hierarchy used for delegation. |
| **Tool** | A capability an agent can invoke: a Python function, another agent, an MCP server tool, an API operation. |
| **ToolContext** | Object injected into tools giving access to state, artifacts, memory search and `actions`. |
| **Runner** | Executes an agent for one user turn: loads the session, drives the event loop, persists events. |
| **Invocation** | Everything that happens in response to one user message, identified by `invocation_id`. |
| **Event** | Immutable record of one thing that happened: a model message, a tool call, a tool result, a state change, a transfer. The session history is a list of events. |
| **EventActions** | Side effects attached to an event: `state_delta`, `artifact_delta`, `transfer_to_agent`, `escalate`, `skip_summarization`. |
| **Session** | One conversation thread: `id`, `app_name`, `user_id`, `events`, `state`. |
| **SessionService** | Stores sessions: in-memory, database (SQL) or Vertex AI. |
| **State** | Key-value scratchpad on a session. Prefixes set scope: none (session), `user:`, `app:`, `temp:`. |
| **Memory** | Long-term, searchable knowledge across sessions, provided by a `MemoryService`. |
| **Artifact** | Named, versioned binary or text blob (file, image, report) attached to a session or user. |
| **Callback** | Function hooked before/after an agent, model call or tool call to observe or change behaviour. |
| **Plugin** | Runner-level bundle of callbacks applied to every agent, tool and model call. |
| **LiteLlm** | ADK model wrapper that routes calls through LiteLLM, enabling Claude, OpenAI and many other providers. |
| **Transfer** | An LLM agent handing control to another agent in the hierarchy via `transfer_to_agent`. |
| **Escalate** | Signal (`actions.escalate = True`) that ends a `LoopAgent` or returns control to a parent. |
| **Eval set** | A file of test cases (user turns, expected tool calls, reference answers) used by `adk eval`. |
| **Trajectory** | The ordered sequence of tool calls an agent made; compared against the expected trajectory in evaluation. |
| **Reasoning path** | Project term: the reconstructed chain of agents, model turns, tool calls and state changes that led to an answer. See [reasoning paths](../07-observability-transparency/reasoning-paths.md). |
| **Run log** | Project term: the structured, persisted record of one invocation. See [run logs](../07-observability-transparency/run-logs.md). |
| **Domain agent** | Project term: a self-contained agent package for one business domain built on the shared core. |
