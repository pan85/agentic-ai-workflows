---
title: Design patterns
tags: [patterns, architecture, multi-agent]
status: draft
---

# Design patterns

## Start simple

One `LlmAgent` with a few good tools solves more than expected. Add agents only
when a single agent's instruction becomes long, contradictory, or needs
different models for different parts.

## Pattern catalogue

| Pattern | Shape | Use when | Watch out for |
| --- | --- | --- | --- |
| Single agent + tools | `LlmAgent(tools=[...])` | Most tasks | Tool count above ~15 hurts selection |
| Coordinator / dispatcher | Router with `sub_agents` | Distinct specialist areas | Vague descriptions cause misrouting |
| Sequential pipeline | `SequentialAgent` | Fixed multi-step process | Errors propagate; validate between steps |
| Fan-out / gather | `ParallelAgent` → synthesiser | Independent lookups | Distinct `output_key`s; cost multiplies |
| Generator / critic | `LoopAgent(generator, critic)` | Quality bar on output | Always set `max_iterations` |
| Planner + helpers | `LlmAgent` with `AgentTool`s | Open-ended decomposition | Harder to evaluate; log the plan |
| Human in the loop | `LongRunningFunctionTool` | Irreversible or costly actions | Persist pending state |
| Deterministic router | Custom `BaseAgent` | Business rules decide the path | Keep LLM out of policy decisions |
| Structured extraction | `output_schema` agent | Feed data to code | No tools on that agent |

## Composition example

```text
SequentialAgent "report_flow"
├── LlmAgent "planner"             output_key=plan
├── ParallelAgent "gather"
│   ├── LlmAgent "web_research"    output_key=web_notes
│   └── LlmAgent "internal_docs"   output_key=doc_notes
├── LoopAgent "write_and_review"   max_iterations=3
│   ├── LlmAgent "writer"          output_key=draft
│   └── LlmAgent "critic"          tools=[approve]  (escalates)
└── LlmAgent "formatter"           output_schema=Report
```

## Model-agnostic patterns

- Assign models by role through the factory; mix providers within one flow.
- Isolate provider-specific features behind `AgentTool`.
- Keep tool schemas flat and simple.
