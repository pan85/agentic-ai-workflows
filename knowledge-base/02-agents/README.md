# 02 · Agents

The agent types ADK provides and how to compose them.

| Note | Summary |
| --- | --- |
| [llm-agents.md](llm-agents.md) | `LlmAgent`: instructions, tools, output keys, schemas, planners |
| [workflow-agents.md](workflow-agents.md) | `SequentialAgent`, `ParallelAgent`, `LoopAgent` for deterministic flow |
| [custom-agents.md](custom-agents.md) | Subclassing `BaseAgent` for arbitrary orchestration logic |
| [multi-agent-systems.md](multi-agent-systems.md) | Agent hierarchies, delegation, transfer and shared state |

## Choosing an agent type

| Need | Use |
| --- | --- |
| Reason, use tools, talk to the user | `LlmAgent` |
| Fixed steps in order | `SequentialAgent` |
| Independent steps at the same time | `ParallelAgent` |
| Repeat until a condition holds | `LoopAgent` |
| Conditional or bespoke control flow | Custom `BaseAgent` |
| Let a model pick a specialist | `LlmAgent` with `sub_agents` |
