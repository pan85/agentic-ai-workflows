---
title: Safety and guardrails
tags: [safety, security, guardrails]
status: draft
---

# Safety and guardrails

Agents act on real systems, so treat them like any service with permissions.

## Threats

| Threat | Example | Mitigation |
| --- | --- | --- |
| Prompt injection | A web page or email tells the agent to export data | Treat tool results as data; restrict tools; confirm sensitive actions |
| Excessive permissions | Agent has write access it never needs | Least-privilege credentials per agent; `tool_filter` on toolsets |
| Irreversible actions | Refunds, deletes, sends | Human approval via `LongRunningFunctionTool`; limits in `before_tool_callback` |
| Data leakage | Secrets in logs or traces | Redaction in the run-log plugin; no secrets in prompts or tool args |
| Runaway loops and cost | Critic never approves | `max_iterations`, timeouts, token budgets per invocation |
| Untrusted code execution | Model-written code runs on host | Sandboxed executors only |

## Layers of control

1. **Instructions**: state limits clearly (weakest layer on its own).
2. **Tool design**: tools that cannot do dangerous things cannot be misused.
3. **Callbacks**: validate arguments, block, or require approval.
4. **Plugins**: global policy applied to every agent.
5. **Infrastructure**: IAM, network egress rules, separate service accounts.

## Human in the loop

For irreversible actions, the tool returns a pending status, the UI asks a
person, and the agent resumes with the decision. The approval and approver are
recorded in the run log.

## Transparency and safety together

Every guardrail decision is logged as a `guardrail` record in the
[run log](../07-observability-transparency/run-logs.md), so blocked actions are
visible and auditable rather than silently swallowed.
