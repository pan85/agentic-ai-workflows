# 0003 · Use ADK events as the source of truth for transparency

- **Status**: Accepted
- **Date**: 2026-10-29

## Context

Execution transparency needs a trustworthy record of state, tool executions and
reasoning paths. A hand-written log can drift from what actually happened.

## Decision

- The persisted session's events are the ground truth.
- A Runner plugin adds a structured run log for data events lack (durations,
  model ids, versions, nested agent steps), keyed by `invocation_id` and trace id.
- State is written only through tracked paths so every change appears as a `state_delta`.
- Reasoning path views are derived from events plus run log, with model
  thoughts labelled as self-reported.

## Consequences

- Persistent session storage is required beyond unit tests.
- Agent code must follow state-writing rules; tests check them.
- Redaction is needed in the run log and traces.
