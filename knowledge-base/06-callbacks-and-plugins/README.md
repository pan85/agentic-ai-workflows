# 06 · Callbacks and plugins

Hooks into the agent lifecycle for control, guardrails and instrumentation.

| Note | Summary |
| --- | --- |
| [callbacks.md](callbacks.md) | The six per-agent callbacks, their signatures and what returning a value does |
| [plugins.md](plugins.md) | Runner-wide plugins for cross-cutting concerns like run logging and policy |

## Callbacks or plugins?

| | Callback | Plugin |
| --- | --- | --- |
| Attached to | One agent | The Runner (every agent, model and tool call) |
| Best for | Agent-specific logic | Logging, tracing, policy, metrics |
| Example | Validate args for one tool | Record every tool call in the run log |
