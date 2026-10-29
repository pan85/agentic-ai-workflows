---
title: Domain catalog
tags: [domains, roadmap]
status: draft
---

# Domain catalog

Candidate domains to exercise different parts of the framework. Update the
status column as work progresses.

| Domain | Purpose | Exercises | Status |
| --- | --- | --- | --- |
| Research assistant | Search, read and summarise sources | AgentTool, parallel fan-out, artifacts | idea |
| Customer support | Triage and resolve support requests | Transfer, guardrails, long-running approval | idea |
| Coding helper | Explain, review and change code | Codex/Claude coding role, MCP filesystem/git tools | idea |
| Data analyst | Query data and produce charts | Code execution, artifacts, structured output | idea |
| Personal productivity | Calendar, email, notes | OAuth tools, memory, `user:` state | idea |
| DevOps assistant | Inspect CI, logs, deployments | OpenAPI/MCP toolsets, read-only safety policy | idea |

## Choosing what to build first

Start with a domain that has:

- simple, safe tools (read-only APIs);
- clear right answers for eval cases;
- at least one multi-step trajectory, so the reasoning path is interesting.

The research assistant fits these well and is a good first domain.
