---
name: orchestrate-gemini
description: Coordinate Gemini CLI sub-agents for substantial multi-step work. Requires Gemini CLI subagents. Pin gemini-3.8-flash with thinking_level high for hard work and thinking_level low for cheap mechanical work. Use proactively when a request involves two or more separable workstreams; repository exploration plus implementation or verification; production diagnosis; PR or release review; multi-source research; or long-running jobs that should not block the root. Skip only trivial single-step or tightly sequential tasks.
---

# Orchestrate

Keep the root agent responsible for decomposition, user communication, approvals, synthesis, and the final claim. Delegate outcomes, not vague help. The root is the only coordinator. Every child is a leaf. A Gemini subagent cannot spawn.

## Decide Whether to Delegate

Delegate only when at least one condition holds:

- Two or more workstreams can proceed independently.
- A slow command, transfer, test suite, or data pull would otherwise make the root unavailable.
- A bounded investigation can gather evidence while the root handles the main path.
- An independent review would materially reduce implementation, production, security, payment, or release risk.

Stay single-agent when the task is small, the next step depends on the immediately previous result, multiple agents would touch the same mutable files, or delegation overhead is comparable to doing the work directly.

## Select the Model

Always pass `model: gemini-3.8-flash`. The split is thinking level, not a different model.

| thinking_level | Use when | Do not use for |
|---|---|---|
| `high` | Ambiguous or expensive-if-wrong work. Implementation, review, architecture, security, payments, a workstream that would cause rework if it is sloppy. | Grep, path lookup, waiting on CI, a test run, copy or export. |
| `low` | Mechanical, deterministic, cheap-if-wrong work. Evidence gathering that is a path or a grep hit, log tails, status polls, exports. | Anything a wrong answer would cause rework. |

Default leaves that need judgment to `high`. Default busywork to `low`. Do not inherit the parent's thinking level.

Pair with a type when the session exposes one: `codebase_investigator` or another read-only explorer for evidence, `generalist` for implementation and reviews that need tools.

## Disclose Every Spawn

Immediately before every subagent call, tell the user:

- The agent task name.
- The type, if any.
- `gemini-3.8-flash` plus `thinking_level` `high` or `low`.
- That the worker is a leaf.

Example:

```text
Spawning:
- `evidence_scout` — codebase_investigator, gemini-3.8-flash thinking_level low — read-only leaf
- `impl_auth` — generalist, gemini-3.8-flash thinking_level high — implementation leaf
```

Describe these as requested configuration. If the runtime reports a different model or thinking level, disclose the correction.

## Use These Reusable Personas

Treat these as assignment shapes, not permanent project agents.

### Evidence scout — thinking_level low

Gather a defined evidence set and return paths, commands, timestamps, exact failures, and confidence. Keep it read-only when the type allows.

### Workflow operator — thinking_level low, or a background command

Run and watch an already-defined, user-authorized job. Do not invent scope or make approval decisions.

### Implementation or review leaf — thinking_level high

Own one bounded code surface or review target. Give distinct file ownership. Report changed files, evidence, and residual risk.

### Senior critic — thinking_level high

Challenge architecture, diagnosis, security, financial correctness, or release readiness. Return a recommendation. Keep the final decision with the root and user.

## Apply Common Delegation Patterns

- **Production incident:** low for live health and logs, high for the relevant code path. The root separates cause from mitigation.
- **PR or release:** low for exact-head diff and CI, high for substantive review. Keep merge and deploy with the root.
- **UI work:** high for implementation. The root verifies in the browser.
- **Long-running job:** background the command so the root stays available. Spawn a low leaf only when something deterministic must be polled.

## Write the Assignment Contract

Pass only the context required to succeed:

1. One concrete objective and why it matters.
2. Exact scope.
3. Ownership boundary.
4. Constraints from the user, repository, and applicable skills.
5. Required validation and return format. If more workers are needed, return a split proposal.
6. Read-only or authorized to mutate.
7. That the worker is a leaf.
8. The exact model and thinking_level disclosed to the user.

Never send two agents overlapping implementation ownership in the same checkout.

## Coordinate and Integrate

- One precise worker is better than forced fan-out.
- Keep a useful path moving locally while agents work, unless the delegated operation is the whole task.
- Treat every result as evidence. Reopen live surfaces before claiming merged, deployed, or fixed.
- Synthesize disagreements. State which evidence wins.
- Preserve user approval for merge, deploy, production, and financial actions.

Return one integrated answer. Do not dump parallel reports on the user.
