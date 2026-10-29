---
title: ToolContext
tags: [tools, state, context]
status: stable
---

# ToolContext

Add a parameter named `tool_context: ToolContext` to a tool and ADK injects it.
It is hidden from the model's view of the tool schema.

```python
from google.adk.tools import ToolContext

def add_to_cart(item_id: str, quantity: int, tool_context: ToolContext) -> dict:
    """Add an item to the user's cart."""
    cart = tool_context.state.get("cart", [])
    cart.append({"item_id": item_id, "quantity": quantity})
    tool_context.state["cart"] = cart          # recorded as a state_delta
    return {"status": "success", "cart_size": len(cart)}
```

## What it offers

| Member | Use |
| --- | --- |
| `state` | Read/write session state. Writes are tracked and committed as `state_delta` on the tool's event. |
| `actions` | `EventActions` for control: `escalate`, `transfer_to_agent`, `skip_summarization`. |
| `function_call_id` | Id linking this execution to the model's function call. |
| `agent_name`, `invocation_id` | Who is running and in which turn. |
| `save_artifact(name, part)` / `load_artifact(name)` / `list_artifacts()` | Work with [artifacts](../04-sessions-state-memory/artifacts.md). Async. |
| `search_memory(query)` | Query the [memory service](../04-sessions-state-memory/memory.md). Async. |
| `request_credential(...)` / `get_auth_response(...)` | OAuth and API-key flows for authenticated tools. |

## Control actions

```python
def finish_review(tool_context: ToolContext) -> dict:
    """Mark the review complete and stop the loop."""
    tool_context.actions.escalate = True
    return {"done": True}

def hand_to_billing(tool_context: ToolContext) -> dict:
    """Transfer the conversation to the billing agent."""
    tool_context.actions.transfer_to_agent = "billing"
    return {"transferred": True}
```

`skip_summarization = True` returns the tool result directly instead of letting
the model rephrase it, which is handy for structured outputs.

## Transparency notes

Prefer state writes through `ToolContext` over global variables or side
channels: they are captured in the event's `state_delta`, so the run log shows
exactly which tool changed which key.
