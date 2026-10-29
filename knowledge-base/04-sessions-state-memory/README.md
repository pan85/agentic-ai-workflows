# 04 · Sessions, state and memory

Where an agent's context lives, from a single turn to a user's whole history.

| Note | Summary |
| --- | --- |
| [sessions.md](sessions.md) | Session object, session services and persistence options |
| [state.md](state.md) | State scopes and prefixes, how and when state is written |
| [memory.md](memory.md) | Long-term memory across sessions |
| [artifacts.md](artifacts.md) | Versioned files and blobs |

## Scope at a glance

| Lifetime | Mechanism |
| --- | --- |
| Current invocation only | `temp:` state |
| One conversation | Session events and unprefixed state |
| All conversations of a user | `user:` state, user-scoped artifacts, memory |
| Whole application | `app:` state |
