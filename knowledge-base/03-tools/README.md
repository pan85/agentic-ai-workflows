# 03 · Tools

How agents act on the world.

| Note | Summary |
| --- | --- |
| [function-tools.md](function-tools.md) | Turning Python functions into tools; signatures, docstrings, return values, long-running tools |
| [tool-context.md](tool-context.md) | `ToolContext`: state, artifacts, memory, auth and control actions from inside a tool |
| [built-in-tools.md](built-in-tools.md) | Google Search, code execution, Vertex AI Search and their constraints |
| [agent-as-tool.md](agent-as-tool.md) | `AgentTool`: calling an agent like a function |
| [mcp-and-openapi.md](mcp-and-openapi.md) | Using MCP servers and OpenAPI specs as toolsets |

## Tool design checklist

- One clear job per tool, named with a verb (`search_orders`, not `orders`).
- Typed parameters with simple JSON-friendly types.
- A docstring that says what it does, when to use it and what it returns.
- Return a `dict` with a `status` field and either data or an error message.
- Fail with an informative result instead of raising, so the model can recover.
- No secrets in arguments or results; they end up in events and traces.
