---
title: Reasoning paths
tags: [observability, reasoning, transparency]
status: draft
---

# Reasoning paths

A **reasoning path** is the readable, step-by-step story of how an invocation
reached its answer. It is built from events (facts) and annotated with model
reasoning (claims).

## What a step contains

| Field | Source |
| --- | --- |
| Step number and timestamp | Event order and `timestamp` |
| Actor | `event.author` |
| Kind | user input, thought, tool call, tool result, transfer, state change, final answer |
| Detail | Call args, result summary, text |
| State changes | `actions.state_delta` |
| Why | Thought/plan text, routing reason in state, custom agent event |

## Example rendering

```text
Invocation 7f3c… · help_desk · model gemini-2.5-flash · 3.4 s

 1  user          "Why was I charged twice in March?"
 2  help_desk     thought: billing question about duplicate charge
 3  help_desk     transfer -> billing            state: route_reason="duplicate charge"
 4  billing       tool_call search_orders(customer_id="C-1042", status="any")
 5  billing       tool_result search_orders -> 2 orders (#881, #882), 41 ms
 6  billing       tool_call get_invoice(order_id="#882")
 7  billing       tool_result get_invoice -> status=duplicate, 37 ms
 8  billing       state: billing:case="duplicate", billing:order="#882"
 9  billing       final: "Order #882 was a duplicate charge. I've flagged it for a refund."
```

## Sources of "why"

1. **Model thoughts**: `BuiltInPlanner(include_thoughts=True)` on Gemini, or
   extended thinking on Claude. Rich but self-reported.
2. **Prompted plans**: `PlanReActPlanner` produces PLANNING/REASONING sections
   on any provider.
3. **Explicit rationale**: instruct routers to state a short reason, or have a
   `route` tool that takes `reason` as an argument. This is the most reliable,
   because it is structured and captured as a tool call.
4. **Code decisions**: custom agents yield an event explaining a branch.

Prefer 3 and 4 for decisions that matter; use 1 and 2 as supporting context.

## Building it

The planned `core/observability` module will:

1. Read a session from the session service.
2. Filter events by `invocation_id`.
3. Classify each event (see [event stream](event-stream.md)).
4. Pair tool calls with responses by call id and add durations from the run log.
5. Replay `state_delta`s to show state before and after each step.
6. Render as text, Markdown or HTML.

## Pitfalls

- Parallel branches: render each `branch` as its own lane.
- AgentTool helpers: their inner steps are not in the parent's events; pull them
  from the run log or trace.
- Do not present model thoughts as facts. Label them.
