---
title: Callbacks
tags: [callbacks, lifecycle, guardrails]
status: stable
---

# Callbacks

Callbacks are functions ADK calls at fixed points of an agent's execution.
They can **observe** (return `None`) or **override** (return a value that
replaces the normal step).

## The six callbacks

| Callback | Signature | Return a value to |
| --- | --- | --- |
| `before_agent_callback` | `(callback_context)` | Skip the agent; the returned `types.Content` is its answer |
| `after_agent_callback` | `(callback_context)` | Replace the agent's output |
| `before_model_callback` | `(callback_context, llm_request)` | Skip the model call; return an `LlmResponse` |
| `after_model_callback` | `(callback_context, llm_response)` | Replace the model's response |
| `before_tool_callback` | `(tool, args, tool_context)` | Skip the tool; the returned `dict` is the result |
| `after_tool_callback` | `(tool, args, tool_context, tool_response)` | Replace the tool result |

Each can also be a list of callbacks, run in order until one returns a value.

## Order within one LLM agent turn

```text
before_agent
  └─ before_model ─▶ model ─▶ after_model
       └─ (function call) before_tool ─▶ tool ─▶ after_tool
  └─ before_model ─▶ model ─▶ after_model   (final text)
after_agent
```

## Examples

### Guardrail: block a tool argument

```python
from typing import Any
from google.adk.tools import BaseTool, ToolContext

def block_large_refunds(tool: BaseTool, args: dict[str, Any], tool_context: ToolContext) -> dict | None:
    if tool.name == "issue_refund" and args.get("amount", 0) > 500:
        tool_context.state["guardrail:last_block"] = f"refund {args['amount']} blocked"
        return {"status": "error", "error_message": "Refunds over 500 need human approval."}
    return None   # allow
```

### Instrumentation: time model calls

```python
import time
from google.adk.agents.callback_context import CallbackContext
from google.adk.models import LlmRequest, LlmResponse

def start_timer(callback_context: CallbackContext, llm_request: LlmRequest) -> None:
    callback_context.state["temp:llm_started"] = time.perf_counter()

def stop_timer(callback_context: CallbackContext, llm_response: LlmResponse) -> None:
    started = callback_context.state.get("temp:llm_started")
    if started is not None:
        ms = (time.perf_counter() - started) * 1000
        print(f"{callback_context.agent_name}: model call took {ms:.0f} ms")
```

```python
agent = LlmAgent(
    name="support", model="gemini-2.5-flash", tools=[issue_refund],
    before_tool_callback=block_large_refunds,
    before_model_callback=start_timer,
    after_model_callback=stop_timer,
)
```

## CallbackContext

Gives `agent_name`, `invocation_id`, mutable `state` (changes become a
`state_delta`), and async `save_artifact` / `load_artifact`.

## Practices

- Keep callbacks fast and side-effect free unless the side effect is the point
  (for example writing a log line).
- Prefer returning a clear error dict over raising.
- Write guardrail decisions to state so they appear in the run log.
- For anything that should apply to every agent, write a [plugin](plugins.md) instead.
