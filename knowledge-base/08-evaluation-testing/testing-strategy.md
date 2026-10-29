---
title: Testing strategy
tags: [testing, quality]
status: draft
---

# Testing strategy

| Layer | What | Model calls | When it runs |
| --- | --- | --- | --- |
| Unit | Tools, callbacks, plugins, custom agent routing, prompt rendering | None | Every commit |
| Integration (offline) | Agent wiring with a fake model returning scripted responses | Fake | Every commit |
| Evaluation | Trajectory and response evals | Real | Pull requests touching agents, nightly |
| Live smoke | One end-to-end call per deployed agent | Real | After deploy |

## Unit tests

Tools are plain functions; test them directly. For tools using `ToolContext`,
pass a light stub exposing `state` and `actions`, or run them through an
`InMemoryRunner`.

```python
def test_block_large_refunds():
    ctx = SimpleNamespace(state={})
    tool = SimpleNamespace(name="issue_refund")
    result = block_large_refunds(tool, {"amount": 900}, ctx)
    assert result["status"] == "error"
    assert "guardrail:last_block" in ctx.state
```

## Offline integration tests

Subclass `BaseLlm` with a scripted model that returns predetermined
`LlmResponse`s (a function call, then text). This tests the agent tree, state
flow and event sequence without network access or cost, and makes the event
stream assertions deterministic:

```python
events = [e async for e in runner.run_async(user_id="u", session_id=sid, new_message=msg)]
authors = [e.author for e in events]
assert authors == ["help_desk", "billing", "billing", "billing"]
```

## Asserting transparency

Tests should check the things the observability layer relies on:

- expected `state_delta` keys appear on the right events;
- routing writes a `route_reason`;
- the run-log plugin emits a `tool_call` record for each tool call;
- secrets are redacted in run-log output.

## Tooling

- `pytest` with `pytest-asyncio`.
- Mark tests that hit real models (`@pytest.mark.live`) and skip them unless an
  API key is present.
