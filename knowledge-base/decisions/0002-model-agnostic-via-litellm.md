# 0002 · Stay model-agnostic via a role-based model factory and LiteLLM

- **Status**: Accepted
- **Date**: 2026-10-29

## Context

We want to use Claude, OpenAI (including Codex-family models) and Gemini, pick
the best model per task, and switch without rewriting agents.

## Decision

- Agents request models by **role** (`router`, `reasoning`, `coding`, `cheap`)
  from `core.models.factory.model_for(role)`.
- Configuration maps roles to model specs, overridable per environment.
- Gemini specs use ADK's native Gemini class; others use `LiteLlm`.

## Alternatives considered

- Hard-coded model strings per agent: simple but switching is a code change.
- Only provider-native wrappers: fewer dependencies, but each provider needs
  bespoke code.

## Consequences

- Swapping a model is a config change, validated by running evals.
- Gemini-only tools must be isolated so agents stay portable.
- LiteLLM becomes a dependency whose provider behaviour we must test.
- The resolved model id is recorded on every call for comparison.
