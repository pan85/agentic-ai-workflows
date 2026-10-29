# Agentic AI Workflows

A model-agnostic agent development framework and knowledge base built on
[Google Agent Development Kit (ADK)](https://google.github.io/adk-docs/).

## Goals

- **Learn** how agentic systems work, from a single tool-calling agent up to multi-agent workflows.
- **Build** multi-domain agents on a shared, reusable core.
- **Version** agents, prompts, tools and evaluations so every change is traceable.
- **Deploy** agents locally, in containers and on managed runtimes.
- **Stay provider-flexible**: swap between Gemini, Claude and OpenAI models without rewriting agents.

## Core focus: execution transparency

Every run should be explainable after the fact. That means clear visibility into:

- **Session state**: what the agent knew and remembered at each step.
- **Tool executions**: which tool ran, with what arguments, what it returned and how long it took.
- **Reasoning paths**: which agent acted, why control moved between agents, and how the final answer was reached.

## Repository layout

| Path | Purpose |
| --- | --- |
| [`knowledge-base/`](knowledge-base/README.md) | Structured notes on ADK, agents, tools, state, providers, observability and deployment |
| [`knowledge-base/decisions/`](knowledge-base/decisions/README.md) | Architecture decision records (ADRs) |

Code (agents, shared core, tests) will be added as the framework grows; the
knowledge base describes the intended structure in
[project layout](knowledge-base/01-adk-fundamentals/project-layout.md).

## Start here

1. Read the [knowledge base index](knowledge-base/README.md).
2. Follow the [learning path](knowledge-base/00-meta/learning-path.md).
3. Check the [glossary](knowledge-base/00-meta/glossary.md) when a term is unclear.
