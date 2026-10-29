---
title: Project layout and CLI
tags: [adk, cli, structure]
status: draft
---

# Project layout and CLI

## ADK agent package

The `adk` CLI discovers agents by folder. Each agent is a Python package that
exposes `root_agent`:

```text
my_agent/
├── __init__.py      # from . import agent
├── agent.py         # defines root_agent
└── .env             # model credentials, not committed
```

Run from the **parent** directory of the package:

| Command | What it does |
| --- | --- |
| `adk create my_agent` | Scaffold a new agent package |
| `adk web` | Dev UI in the browser: chat, events, traces, state, eval |
| `adk run my_agent` | Interactive terminal chat |
| `adk api_server` | FastAPI server exposing the agents over HTTP |
| `adk eval my_agent path/to/evalset.json` | Run an eval set |
| `adk deploy cloud_run ...` | Build and deploy to Cloud Run |
| `adk deploy agent_engine ...` | Deploy to Vertex AI Agent Engine |

## Environment

```bash
# Gemini via Google AI Studio
GOOGLE_GENAI_USE_VERTEXAI=FALSE
GOOGLE_API_KEY=...

# Gemini (and Claude) via Vertex AI
GOOGLE_GENAI_USE_VERTEXAI=TRUE
GOOGLE_CLOUD_PROJECT=...
GOOGLE_CLOUD_LOCATION=us-central1

# Other providers via LiteLLM
ANTHROPIC_API_KEY=...
OPENAI_API_KEY=...
```

Never commit `.env`. Commit a `.env.example` with the variable names only.

## Planned repository layout

The framework separates a shared core from domain agents so that providers,
observability and tooling are written once:

```text
agentic-ai-workflows/
├── core/                    # shared, domain-agnostic code
│   ├── models/              # provider registry and model factory
│   ├── observability/       # run-log plugin, tracing setup, exporters
│   ├── tools/               # reusable tools
│   └── sessions/            # session/memory service factories
├── agents/                  # one package per domain agent (adk discovers these)
│   └── <domain>_agent/
│       ├── __init__.py
│       ├── agent.py         # root_agent
│       ├── prompts.py       # versioned instructions
│       ├── tools.py         # domain tools
│       └── evals/           # eval sets for this agent
├── tests/                   # unit and integration tests
├── knowledge-base/          # this documentation
└── pyproject.toml
```

Run `adk web agents/` to see every domain agent in the dev UI.

## See also

- [Domain agent template](../10-domain-agents/domain-agent-template.md)
- [Model factory](../05-model-providers/model-abstraction.md)
