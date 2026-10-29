---
title: ADK dev UI
tags: [observability, tooling, adk-web]
status: stable
---

# ADK dev UI (`adk web`)

`adk web <agents_dir>` starts a local web app for chatting with agents and
inspecting runs. It is the quickest way to see transparency data while building.

## What it shows

| View | Use |
| --- | --- |
| Chat | Talk to the selected agent; tool calls and results show inline |
| Events | Every event of the session with its raw JSON, including `actions` |
| Trace | Per-invocation span timeline: agent runs, model calls, tool calls |
| State | Current session state |
| Artifacts | Saved artifacts and versions |
| Sessions | Switch between or create sessions |
| Eval | Save the current session as an eval case and run eval sets |
| Graph | Visual agent tree for multi-agent apps |

## Tips

- Use `--session_service_uri sqlite:///./sessions.db` (or a Postgres URI) to
  keep sessions across restarts so you can inspect older runs.
- Click a model event to see the exact request sent to the model, which is the
  fastest way to debug instructions and state templating.
- Use the Eval tab to turn a good manual run into a regression test.
- `adk web` is for development only; do not expose it publicly.

> Version note: available panels and flags change between releases. Run
> `adk web --help`.
