---
title: ADK evaluation
tags: [evaluation, testing]
status: draft
---

# ADK evaluation

ADK evaluates two things:

1. **Trajectory**: did the agent call the expected tools, with the expected
   arguments, in the expected order?
2. **Response**: is the final answer close to a reference answer?

Trajectory evaluation is especially valuable for us because it tests the
*path*, not just the output, which is execution transparency turned into tests.

## Eval files

- **Test file** (`*.test.json`): a small set of cases for one agent, run as
  unit-style tests during development.
- **Eval set** (`*.evalset.json`): larger, multi-turn sessions for integration
  evaluation.

Each case holds user turns, the expected tool uses (`tool_name`, `tool_input`)
and a reference final response. Create them by hand or save a session from the
dev UI's Eval tab.

> Version note: the eval file schema changed in ADK 1.x (Pydantic-backed
> `EvalSet`/`EvalCase`). Generate files with the dev UI to get the current shape.

## Criteria

`test_config.json` next to the test file sets thresholds:

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0,
    "response_match_score": 0.8
  }
}
```

| Criterion | Meaning |
| --- | --- |
| `tool_trajectory_avg_score` | Fraction of cases whose tool trajectory matches exactly. 1.0 is strict. |
| `response_match_score` | ROUGE-style similarity of the final answer to the reference. |

Newer versions add LLM-judged criteria (for example final response quality and
hallucination checks). Use them for open-ended answers where text similarity is
a poor signal.

## Running

```bash
adk eval agents/billing_agent agents/billing_agent/evals/refunds.evalset.json
```

```python
# tests/eval/test_billing_eval.py
import pytest
from google.adk.evaluation.agent_evaluator import AgentEvaluator

@pytest.mark.asyncio
async def test_billing_refunds():
    await AgentEvaluator.evaluate(
        agent_module="agents.billing_agent",
        eval_dataset_file_path_or_dir="agents/billing_agent/evals/refunds.test.json",
    )
```

## Practices

- Write eval cases before swapping models or rewriting instructions.
- Cover each important trajectory, plus refusal and error cases.
- Keep tools deterministic in evals (fakes or recorded responses) so failures
  point at the agent, not the backend.
- Run evals for each provider in the [capability matrix](../05-model-providers/provider-selection.md)
  before declaring an agent portable.
- Turn interesting production runs (from [run logs](../07-observability-transparency/run-logs.md))
  into new cases.
