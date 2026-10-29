# Knowledge Base Index

This knowledge base is the project's shared brain: concepts, how-tos, patterns
and decisions for building transparent, model-agnostic agents on Google ADK.

Sections are numbered in the order you would normally learn them. Each folder
has its own `README.md` that lists and summarises its notes.

## Map

| # | Section | What it covers |
| --- | --- | --- |
| 00 | [Meta](00-meta/README.md) | Conventions, note template, glossary, learning path |
| 01 | [ADK fundamentals](01-adk-fundamentals/README.md) | What ADK is, runtime architecture, project layout and CLI |
| 02 | [Agents](02-agents/README.md) | LLM agents, workflow agents, custom agents, multi-agent systems |
| 03 | [Tools](03-tools/README.md) | Function tools, built-in tools, agent-as-tool, MCP/OpenAPI, ToolContext |
| 04 | [Sessions, state and memory](04-sessions-state-memory/README.md) | Sessions, state scopes, long-term memory, artifacts |
| 05 | [Model providers](05-model-providers/README.md) | Provider abstraction, Gemini, Claude, OpenAI, choosing a model |
| 06 | [Callbacks and plugins](06-callbacks-and-plugins/README.md) | Lifecycle hooks for control, guardrails and instrumentation |
| 07 | [Observability and transparency](07-observability-transparency/README.md) | Event stream, tracing, reasoning paths, run logs, dev UI |
| 08 | [Evaluation and testing](08-evaluation-testing/README.md) | ADK evaluation, test pyramid for agents |
| 09 | [Deployment](09-deployment/README.md) | Local, containers, Cloud Run, Agent Engine, versioning |
| 10 | [Domain agents](10-domain-agents/README.md) | How to add a new domain agent on the shared core |
| 11 | [Patterns and practices](11-patterns-and-practices/README.md) | Design patterns, prompting, safety and guardrails |
| — | [Decisions](decisions/README.md) | Architecture decision records |

## Hierarchy

```text
knowledge-base/
├── 00-meta/                       how this knowledge base works
├── 01-adk-fundamentals/           the framework itself
├── 02-agents/                     building blocks: agents
├── 03-tools/                      building blocks: tools
├── 04-sessions-state-memory/      building blocks: context and memory
├── 05-model-providers/            model-agnostic layer
├── 06-callbacks-and-plugins/      hooks into the agent lifecycle
├── 07-observability-transparency/ core focus: seeing what happened
├── 08-evaluation-testing/         proving agents behave
├── 09-deployment/                 shipping and versioning
├── 10-domain-agents/              applying the framework per domain
├── 11-patterns-and-practices/     cross-cutting know-how
└── decisions/                     ADRs
```

## How to use it

- **Learning**: follow the [learning path](00-meta/learning-path.md).
- **Building**: jump to the section for the building block you need, then to
  [patterns](11-patterns-and-practices/README.md).
- **Debugging a run**: start at [observability](07-observability-transparency/README.md).
- **Adding notes**: follow the [conventions](00-meta/conventions.md) and copy the
  [note template](00-meta/note-template.md).

> ADK moves fast. Code samples target the Python `google-adk` 1.x API. When a
> note depends on a detail that may change, it says so; check the
> [official docs](https://google.github.io/adk-docs/) before relying on it.
