---
title: Local development
tags: [deployment, local, setup]
status: draft
---

# Local development

## Setup

```bash
python -m venv .venv && source .venv/bin/activate
pip install google-adk litellm
cp .env.example .env            # fill in keys
```

Python 3.10+ is required (check ADK's current minimum).

## Run modes

| Command | Use |
| --- | --- |
| `adk web agents/` | Dev UI with events, trace and state |
| `adk run agents/billing_agent` | Quick terminal chat |
| `adk api_server agents/` | Local HTTP API to integrate a front end |
| `python scripts/run_once.py` | Scripted Runner for reproducible debugging |

## API server endpoints (selection)

- `POST /apps/{app}/users/{user}/sessions` create a session
- `POST /run` run one turn and return all events
- `POST /run_sse` same, streamed as server-sent events
- `GET /list-apps` list discovered agents

Check `/docs` on the running server for the full OpenAPI description.

## Tips

- Use a SQLite session store locally so you can inspect old runs.
- Run a local trace backend (see [tracing](../07-observability-transparency/tracing.md)).
- Keep `.env` out of git; commit `.env.example`.
