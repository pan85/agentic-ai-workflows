---
title: Sessions
tags: [sessions, persistence]
status: stable
---

# Sessions

A `Session` is one conversation thread between a user and an application.

| Field | Meaning |
| --- | --- |
| `id` | Session id |
| `app_name` | Which application (agent) it belongs to |
| `user_id` | Which user |
| `events` | Ordered list of every [event](../07-observability-transparency/event-stream.md) |
| `state` | Key-value [state](state.md) |
| `last_update_time` | Timestamp of the last event |

## Session services

| Service | Storage | Use for |
| --- | --- | --- |
| `InMemorySessionService` | Process memory | Tests, notebooks, `adk web` |
| `DatabaseSessionService` | SQL via SQLAlchemy (SQLite, PostgreSQL, MySQL) | Self-hosted production |
| `VertexAiSessionService` | Vertex AI Agent Engine | Managed deployments on Google Cloud |

```python
from google.adk.sessions import DatabaseSessionService

sessions = DatabaseSessionService(db_url="sqlite+aiosqlite:///./sessions.db")
# PostgreSQL needs an async driver:
# DatabaseSessionService(db_url="postgresql+asyncpg://user:pass@host/db")
```

> Version note: recent ADK versions use async SQLAlchemy, so database URLs need
> an async driver (`aiosqlite`, `asyncpg`). Install the driver package
> separately.

## Core operations (async)

```python
session = await sessions.create_session(app_name="demo", user_id="u1", state={"tier": "gold"})
session = await sessions.get_session(app_name="demo", user_id="u1", session_id=session.id)
listing = await sessions.list_sessions(app_name="demo", user_id="u1")
await sessions.delete_session(app_name="demo", user_id="u1", session_id=session.id)
```

`append_event` is called by the Runner; you rarely call it yourself.

## Practices

- Use `InMemorySessionService` in unit tests and a database in anything you
  want to inspect later. **A run you cannot reload is a run you cannot explain.**
- Do not mutate a `Session` object you fetched to change state; changes made
  that way are not persisted. Write state through context objects or events.
- Long sessions grow token cost. Summarise old turns, or start a new session
  and carry key facts in `user:` state or memory.
- Pass sensible `user_id`s from your auth layer; `user:` state and memory are
  keyed on it.

## Transparency notes

The persisted session is the primary audit trail. Our observability tools read
from the session service so that any past run can be replayed and rendered.
