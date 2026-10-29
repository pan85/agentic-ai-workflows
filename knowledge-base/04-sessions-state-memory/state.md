---
title: State
tags: [state, sessions]
status: stable
---

# State

`session.state` is a dictionary the agents and tools share. Values must be
JSON-serialisable (strings, numbers, booleans, lists, dicts).

## Scopes by prefix

| Prefix | Scope | Persisted | Example |
| --- | --- | --- | --- |
| *(none)* | This session | With persistent services | `state["cart"]` |
| `user:` | All sessions of this user in this app | Yes | `state["user:language"]` |
| `app:` | All users and sessions of this app | Yes | `state["app:feature_flags"]` |
| `temp:` | Current invocation only | Never | `state["temp:raw_api_response"]` |

## How to write state

State changes must flow through an event so the session service can persist
and record them.

1. **`output_key`** on an `LlmAgent`: final text saved under that key.
2. **`tool_context.state[...] = ...`** inside a tool.
3. **`callback_context.state[...] = ...`** inside a callback.
4. **`EventActions(state_delta={...})`** on an event you yield from a custom agent.
5. **`create_session(state=...)`** for initial values.

Each of these ends up as an `event.actions.state_delta`, which is exactly what
makes state changes traceable.

**Do not** do `session = await svc.get_session(...); session.state["x"] = 1`.
That bypasses event tracking and is not saved.

## Reading state

- In instructions: `{key}` and `{key?}` templating.
- In tools: `tool_context.state.get("key")`.
- In custom agents: `ctx.session.state`.

## Naming conventions for this project

- Namespace by domain or agent: `orders:last_query`, `research:facts`.
- Keep large blobs out of state; use [artifacts](artifacts.md).
- Use `temp:` for scratch data that must not leak into later turns.
- Document the keys each domain agent reads and writes in its README.

## Transparency notes

Replaying the `state_delta` of each event in order reconstructs the state at
any point in the run. The [run log](../07-observability-transparency/run-logs.md)
stores these snapshots so you can ask "what did the agent know at step N?".
