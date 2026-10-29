---
title: Event stream
tags: [observability, events]
status: stable
---

# Event stream

An `Event` is the atomic record of something that happened during an
invocation. The session's `events` list is an append-only log of the
conversation.

## Event fields

| Field | Meaning |
| --- | --- |
| `id` | Unique event id |
| `invocation_id` | Groups all events of one user turn |
| `author` | `"user"` or the agent `name` that produced it |
| `timestamp` | When it was created |
| `content` | `types.Content` with parts: text, `function_call`, `function_response`, thought, inline data |
| `partial` | `True` for streaming chunks (not persisted) |
| `turn_complete` | Live streaming marker |
| `branch` | Path in the agent tree, used to isolate parallel branches |
| `actions` | `EventActions` (below) |
| `error_code`, `error_message` | Populated when a model or tool failed |
| `usage_metadata` | Token counts for model events, when available |

### EventActions

| Field | Meaning |
| --- | --- |
| `state_delta` | Keys and values written by this step |
| `artifact_delta` | Artifact filename and new version |
| `transfer_to_agent` | Target agent of a transfer |
| `escalate` | Exit a loop or return to parent |
| `skip_summarization` | Return tool result without model rephrasing |
| `requested_auth_configs` | Tool asked for credentials |

## Classifying an event

```python
def describe(event) -> str:
    if calls := event.get_function_calls():
        return "tool_call: " + ", ".join(f"{c.name}({c.args})" for c in calls)
    if resps := event.get_function_responses():
        return "tool_result: " + ", ".join(f"{r.name} -> {r.response}" for r in resps)
    if event.actions and event.actions.transfer_to_agent:
        return f"transfer -> {event.actions.transfer_to_agent}"
    if event.content and event.content.parts:
        texts = [p.text for p in event.content.parts if p.text and not p.thought]
        thoughts = [p.text for p in event.content.parts if p.text and p.thought]
        if thoughts:
            return "thought: " + " ".join(thoughts)
        if texts:
            kind = "final" if event.is_final_response() else "text"
            return f"{kind}: " + " ".join(texts)
    if event.actions and event.actions.state_delta:
        return f"state: {event.actions.state_delta}"
    return "other"
```

## Replaying a past run

```python
session = await sessions.get_session(app_name="demo", user_id="u1", session_id=sid)
turn = [e for e in session.events if e.invocation_id == wanted_invocation]
for e in turn:
    print(e.timestamp, e.author, describe(e))
```

## Transparency notes

- `is_final_response()` is the reliable way to find the answer shown to the user.
- Function calls and their responses are linked by `id` (call id), which lets
  you pair them even when the model makes parallel calls.
- Events from parallel branches interleave; group by `branch` to read each one.
