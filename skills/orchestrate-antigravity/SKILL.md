---
name: orchestrate-antigravity
description: Coordinate Antigravity sub-agents for substantial multi-step work. Requires invoke_subagent. Pin Gemini 3.8 Flash thinking high for hard work and thinking low for cheap mechanical work. Use proactively when a request involves two or more separable workstreams; repository exploration plus implementation or verification; production diagnosis; PR or release review; multi-source research; or long-running jobs that should not block the root. Skip only trivial single-step or tightly sequential tasks.
---

# Orchestrate

Keep the root agent responsible for decomposition, user communication, approvals, synthesis, and the final claim. Delegate outcomes, not vague help. The root is the only coordinator. Every child is a leaf with a self-contained prompt.

## Decide Whether to Delegate

Delegate only when at least one condition holds:

- Two or more workstreams can proceed independently.
- A slow command, transfer, test suite, or data pull would otherwise make the root unavailable.
- A bounded investigation can gather evidence while the root handles the main path.
- An independent review would materially reduce implementation, production, security, payment, or release risk.

Stay single-agent when the task is small, the next step depends on the immediately previous result, multiple agents would touch the same mutable files, or delegation overhead is comparable to doing the work directly.

## Select the Model

Always pass Gemini 3.8 Flash. The split is thinking level. Prefer the Antigravity slugs `gemini-3.8-flash-high` and `gemini-3.8-flash-low`. They collapse to `gemini-3.8-flash` with that thinking level. If the tool only accepts `flash` / `pro` / `inherit`, pass `flash` and set thinking_level in the spawn config. Do not inherit.

| Slug | Use when | Do not use for |
|---|---|---|
| `gemini-3.8-flash-high` | Ambiguous or expensive-if-wrong work. Implementation, review, architecture, security, payments. | Grep, path lookup, waiting on CI, a test run, copy or export. |
| `gemini-3.8-flash-low` | Mechanical, deterministic, cheap-if-wrong work. Evidence that is a path or a grep hit, log tails, status polls, exports. | Anything a wrong answer would cause rework. |

Workspace: `inherit` when this worker has exclusive files. `branch` (isolated worktree) when another writer is live or the parent is also editing.

## Disclose Every Spawn

Immediately before every `invoke_subagent` call, tell the user:

- The agent task name and role.
- The model slug (`gemini-3.8-flash-high` or `gemini-3.8-flash-low`).
- Workspace (`inherit` or `branch`).
- That the worker is a leaf.

Example:

```text
Spawning:
- `evidence_scout` — gemini-3.8-flash-low, workspace inherit — read-only leaf
- `impl_auth` — gemini-3.8-flash-high, workspace branch — implementation leaf
```

Describe these as requested configuration. If the runtime reports a different slug or workspace, disclose the correction.

## Use These Reusable Personas

Treat these as assignment shapes, not permanent project agents.

### Evidence scout — gemini-3.8-flash-low

Gather a defined evidence set and return paths, commands, timestamps, exact failures, and confidence. Keep writes off.

### Workflow operator — gemini-3.8-flash-low, or a background task

Run and watch an already-defined, user-authorized job. Do not invent scope or make approval decisions.

### Implementation or review leaf — gemini-3.8-flash-high

Own one bounded code surface or review target. Give distinct file ownership. Use `branch` if another writer is live. Report changed files, evidence, residual risk, and the worktree path when isolated.

### Senior critic — gemini-3.8-flash-high

Challenge architecture, diagnosis, security, financial correctness, or release readiness. Return a recommendation. Keep the final decision with the root and user.

## Apply Common Delegation Patterns

- **Production incident:** low for live health and logs, high for the relevant code path. The root separates cause from mitigation.
- **PR or release:** low for exact-head diff and CI, high for substantive review. Keep merge and deploy with the root.
- **UI work:** high for implementation. `branch` if another writer is live. The root verifies in the browser.
- **Long-running job:** background the task so the root stays available. Spawn a low leaf only when something deterministic must be polled.

## Write the Assignment Contract

Pass only the context required to succeed:

1. One concrete objective and why it matters.
2. Exact scope.
3. Ownership boundary.
4. Constraints from the user, repository, and applicable skills.
5. Required validation and return format. If more workers are needed, return a split proposal.
6. Read-only or authorized to mutate.
7. That the worker is a leaf.
8. The exact model slug and workspace disclosed to the user.

Worktree edits stay in the child's tree until they are applied to the parent. The root does not claim the parent has those edits until that apply happens.

## Coordinate and Integrate

- One precise worker is better than forced fan-out.
- Keep a useful path moving locally while agents work, unless the delegated operation is the whole task.
- Treat every result as evidence. Reopen live surfaces before claiming merged, deployed, or fixed.
- Synthesize disagreements. State which evidence wins.
- Preserve user approval for merge, deploy, production, and financial actions.

Return one integrated answer. Do not dump parallel reports on the user.
