---
title: Transparency principles
tags: [observability, transparency, principles]
status: stable
---

# Transparency principles

Execution transparency means **any run can be explained after the fact without
re-running it**. These principles guide how we build agents and tooling.

## 1. Events are the source of truth

ADK already records every step as an [event](event-stream.md). We never keep a
parallel, hand-written log that can drift; everything we render derives from
events plus telemetry.

## 2. Every state change has an author

State must change only through tracked paths (`output_key`, `ToolContext`,
`CallbackContext`, `EventActions`). Then each change is attached to the event,
agent and tool that made it. See [state](../04-sessions-state-memory/state.md).

## 3. Every tool call is fully recorded

Name, arguments, result, duration, error, and which agent called it. Large
payloads go to artifacts with a reference in the record.

## 4. Decisions are explicit

When control moves (transfer, routing, loop exit), the reason is captured: a
routing state key, a short rationale from the model, or a custom agent event.
"The model decided" is not an explanation.

## 5. Model reasoning is labelled as such

Thoughts and plans from the model are useful but self-reported. Display them
separately from observed facts (tool calls, state) and never treat them as proof.

## 6. Runs are reproducible enough

Record the model id, generation config, instruction version, tool versions and
agent version on every run so differences between runs can be traced.

## 7. Transparency does not leak secrets

Redact credentials and personal data in run logs and traces. Transparency is for
the people operating the system; apply access control to it.

## Definition of done for a new agent

- [ ] Uses persistent sessions outside of unit tests.
- [ ] Writes state only through tracked paths, with namespaced keys.
- [ ] Tools return structured results with `status`.
- [ ] Routing decisions store a reason.
- [ ] Run log plugin enabled; a sample run renders as a readable reasoning path.
- [ ] Has an eval set covering its main trajectories.
