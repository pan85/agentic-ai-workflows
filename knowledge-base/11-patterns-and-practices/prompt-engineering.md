---
title: Prompt engineering for agents
tags: [prompts, instructions]
status: draft
---

# Prompt engineering for agents

Instructions are code: version them, test them with evals, review changes.

## Instruction structure

1. **Role and scope**: who the agent is and what it does *not* do.
2. **Goals**: what a good outcome looks like.
3. **Tool rules**: when to use each tool, in what order, and what to do on errors.
4. **Output format**: length, tone, structure.
5. **Transparency rule**: state the reason for decisions in one sentence.

## Tool descriptions

The model sees each tool's name, docstring and parameter schema. Make the
docstring answer: what it does, when to use it, what each argument means with an
example, and what it returns.

## Cross-provider tips

- Be explicit. Different models fill gaps differently; explicit rules behave
  consistently.
- Avoid provider-specific syntax in shared instructions.
- Keep instructions short; put reference data in state and template it in.
- Give one or two examples of correct tool use for tricky tools.
- Test the same instruction on every provider the agent may run on.

## Anti-patterns

- "Think step by step" with no structure: use a planner instead.
- Hiding business rules in prose: enforce them in code or callbacks.
- Instructions that contradict tool docstrings.
- Unversioned edits made directly in production.
