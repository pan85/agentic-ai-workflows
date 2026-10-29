# 07 · Observability and transparency

The project's core focus: after any run, we can see what the agent knew, what it
did, and why. This section defines the sources of truth and how we turn them
into readable views.

| Note | Summary |
| --- | --- |
| [transparency-principles.md](transparency-principles.md) | What "execution transparency" means here and the three pillars |
| [event-stream.md](event-stream.md) | ADK events as the ground-truth record of a run |
| [tracing.md](tracing.md) | OpenTelemetry spans, Cloud Trace and third-party backends |
| [reasoning-paths.md](reasoning-paths.md) | Reconstructing the step-by-step path to an answer |
| [run-logs.md](run-logs.md) | The project's structured run-log format |
| [dev-ui.md](dev-ui.md) | Using `adk web` to inspect events, state and traces |

## Three pillars

| Pillar | Question it answers | Source |
| --- | --- | --- |
| Session state | What did the agent know at step N? | `state_delta` on events, session snapshots |
| Tool executions | What did it do, with which inputs, and what came back? | function call/response events, tool spans |
| Reasoning paths | Why did it take that path? | agent authorship, transfers, thoughts/plans, routing state |
