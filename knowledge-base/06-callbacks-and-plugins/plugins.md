---
title: Plugins
tags: [plugins, lifecycle, observability]
status: draft
---

# Plugins

A plugin subclasses `BasePlugin` and is registered on the Runner. Its hooks run
for **every** agent, model call and tool call in the application, which makes
plugins the right home for cross-cutting concerns.

> Version note: plugins were added in ADK 1.x (around 1.7). Hook names below
> match the docs at that time; check `google.adk.plugins.base_plugin` for the
> current list.

## Hooks

| Level | Hooks |
| --- | --- |
| Runner | `on_user_message_callback`, `before_run_callback`, `on_event_callback`, `after_run_callback` |
| Agent | `before_agent_callback`, `after_agent_callback` |
| Model | `before_model_callback`, `after_model_callback`, `on_model_error_callback` |
| Tool | `before_tool_callback`, `after_tool_callback`, `on_tool_error_callback` |

Hooks are `async` and receive keyword arguments. Returning a value short-circuits
the step, as with callbacks. Plugin hooks run **before** agent-level callbacks.

## Example: tool call recorder

```python
import time
from typing import Any
from google.adk.plugins.base_plugin import BasePlugin
from google.adk.tools import BaseTool, ToolContext

class ToolRecorder(BasePlugin):
    def __init__(self) -> None:
        super().__init__(name="tool_recorder")
        self._started: dict[str, float] = {}
        self.records: list[dict[str, Any]] = []

    async def before_tool_callback(self, *, tool: BaseTool, tool_args: dict[str, Any],
                                   tool_context: ToolContext) -> dict | None:
        self._started[tool_context.function_call_id] = time.perf_counter()
        return None

    async def after_tool_callback(self, *, tool: BaseTool, tool_args: dict[str, Any],
                                  tool_context: ToolContext, result: dict) -> dict | None:
        started = self._started.pop(tool_context.function_call_id, time.perf_counter())
        self.records.append({
            "invocation_id": tool_context.invocation_id,
            "agent": tool_context.agent_name,
            "tool": tool.name,
            "args": tool_args,
            "result": result,
            "duration_ms": round((time.perf_counter() - started) * 1000, 1),
        })
        return None
```

```python
from google.adk.runners import Runner

runner = Runner(agent=root_agent, app_name="demo",
                session_service=sessions, plugins=[ToolRecorder()])
```

## Plugins we plan to build in `core/observability`

- **RunLogPlugin**: writes the structured [run log](../07-observability-transparency/run-logs.md).
- **PolicyPlugin**: global guardrails (blocked tools, PII redaction in logs).
- **CostPlugin**: token usage and cost per model call from `llm_response.usage_metadata`.

ADK also ships a `LoggingPlugin` that prints lifecycle events, which is useful
while developing.
