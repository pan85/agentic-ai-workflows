---
title: Function tools
tags: [tools]
status: stable
---

# Function tools

Any Python function (sync or async) passed in `tools=[...]` becomes a
`FunctionTool`. ADK builds the tool schema the model sees from the function's
**name, type hints and docstring**, so those are part of your prompt.

## Anatomy of a good tool

```python
def search_orders(customer_id: str, status: str = "any", limit: int = 10) -> dict:
    """Find a customer's orders.

    Use this when the user asks about past or current orders.

    Args:
        customer_id: The customer's id, for example "C-1042".
        status: One of "open", "shipped", "cancelled" or "any".
        limit: Maximum number of orders to return.

    Returns:
        {"status": "success", "orders": [...]} or
        {"status": "error", "error_message": "..."}.
    """
    if not customer_id.startswith("C-"):
        return {"status": "error", "error_message": f"Invalid customer id {customer_id!r}"}
    orders = _db.find_orders(customer_id, status, limit)
    return {"status": "success", "orders": orders}
```

## Rules of thumb

- **Parameters**: use `str`, `int`, `float`, `bool`, `list`, `dict` or Pydantic
  models. Avoid `*args`, `**kwargs` and default values the model cannot reason
  about.
- **Returns**: prefer `dict`. A non-dict value is wrapped as `{"result": value}`.
- **Errors**: return them, do not raise. A raised exception stops the turn; a
  returned error lets the model retry or explain.
- **Async**: `async def` tools are awaited; use them for I/O.
- **Size**: keep results small. Large payloads cost tokens and clutter traces;
  save them as an [artifact](../04-sessions-state-memory/artifacts.md) and
  return a reference.

## Long-running tools

For work that takes minutes or needs human approval, wrap the function in
`LongRunningFunctionTool`. The tool returns an initial status (for example a
ticket id), the agent run pauses, and your application later sends the final
`FunctionResponse` back to the agent.

```python
from google.adk.tools import LongRunningFunctionTool

def request_approval(amount: float, reason: str) -> dict:
    """Ask a manager to approve a refund. Returns a pending ticket."""
    ticket = approvals.create(amount=amount, reason=reason)
    return {"status": "pending", "ticket_id": ticket.id}

approval_tool = LongRunningFunctionTool(func=request_approval)
```

This is the main building block for human-in-the-loop flows.

## Transparency notes

- Each call produces a `function_call` event (name, args, call id) and a
  `function_response` event; both are persisted on the session.
- Tool spans (`execute_tool <name>`) appear in OpenTelemetry traces.
- Use `before_tool_callback` / `after_tool_callback` to record timing and
  validate arguments. See [callbacks](../06-callbacks-and-plugins/callbacks.md).
