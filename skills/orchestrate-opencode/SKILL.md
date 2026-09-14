---
name: orchestrate-opencode
description: Coordinate OpenCode sub-agents for substantial multi-step work on the Go plan. Requires OpenCode subagents (General, Explore, Scout). Pin opencode-go/kimi-k3 for high-effort work, opencode-go/glm-5.3-flash for mid leaves, and opencode-go/deepseek-v4.1-flash for cheap mechanical work. Use proactively when a request involves two or more separable workstreams; repository exploration plus implementation or verification; production diagnosis; PR or release review; multi-source research; or long-running jobs that should not block the root. Skip only trivial single-step or tightly sequential tasks.
---

# Orchestrate

Keep the root agent responsible for decomposition, user communication, approvals, synthesis, and the final claim. Delegate outcomes, not vague help.

## Decide Whether to Delegate

Delegate only when at least one condition holds:

- Two or more workstreams can proceed independently.
- A slow command, transfer, test suite, or data pull would otherwise make the root unavailable.
- A bounded investigation can gather evidence while the root handles the main path.
- An independent review would materially reduce implementation, production, security, payment, or release risk.

Stay single-agent when the task is small, the next step depends on the immediately previous result, multiple agents would touch the same mutable files, or delegation overhead is comparable to doing the work directly.

## Select the Model

Always pass an OpenCode Go id. Do not inherit. Format is `opencode-go/<id>`.

| Model | Use when | Do not use for |
|---|---|---|
| `opencode-go/kimi-k3` | High-effort, ambiguous, or expensive-if-wrong work. Architecture, security, payments, a hard implementation, a review you should not rubber-stamp, a workstream that needs a stronger coding peer. | Grep, path lookup, waiting on CI, a test run, copy or export. |
| `opencode-go/glm-5.3-flash` | Ordinary bounded leaves. Scoped implementation, a normal review, evidence that needs judgment, reconciling a few sources. | Final high-stakes calls. Mechanical busywork that DeepSeek can finish. |
| `opencode-go/deepseek-v4.1-flash` | Cheap mechanical work. Explore/Scout file search, log grep, status polls, exports, a test run whose output is pass/fail. | Ambiguous work, implementation, review. |

Default real leaves to GLM-5.3-Flash. Escalate to Kimi K3 when a weak result would cause rework. Use DeepSeek V4.1 Flash only for busywork.

Pair with a built-in type:

- `Explore` or `Scout` plus DeepSeek for read-only lookup. Scout for external docs and dependencies. Explore for the repo.
- `General` plus GLM for ordinary implementation and review.
- `General` plus Kimi K3 for high-stakes work.

Tell Explore, Scout, GLM, and DeepSeek they are leaves. Kimi K3 `General` may coordinate further only when the root explicitly assigns that. Otherwise it is a leaf too.

## Disclose Every Spawn

Immediately before every subagent call, tell the user:

- The agent task name.
- The type (`Explore`, `Scout`, `General`).
- The exact `opencode-go/...` id.
- Leaf or authorized coordinator.

Example:

```text
Spawning:
- `evidence_scout` — Explore, opencode-go/deepseek-v4.1-flash — read-only leaf
- `impl_auth` — General, opencode-go/glm-5.3-flash — implementation leaf
- `senior_critic` — General, opencode-go/kimi-k3 — senior-review leaf
```

Describe these as requested configuration. If the runtime reports a different model, disclose the correction.

## Use These Reusable Personas

Treat these as assignment shapes, not permanent project agents.

### Evidence scout — Explore or Scout, DeepSeek V4.1 Flash

Gather a defined evidence set and return paths, commands, timestamps, exact failures, and confidence. Keep it read-only.

### Workflow operator — DeepSeek V4.1 Flash, or a background command

Run and watch an already-defined, user-authorized job. Do not invent scope or make approval decisions.

### Implementation or review leaf — General, GLM-5.3-Flash

Own one bounded code surface or review target. Give distinct file ownership. Report changed files, evidence, and residual risk.

### Workstream owner or senior critic — General, Kimi K3

Own a substantial independent workstream, or challenge architecture, diagnosis, security, financial correctness, or release readiness. Coordinate children only when the root authorized it. Return one integrated result or a recommendation. Keep the final decision with the root and user.

## Apply Common Delegation Patterns

- **Production incident:** DeepSeek Explore for live health and logs, GLM General for the relevant code path, Kimi K3 only if the investigation becomes a coordinated workstream. The root separates cause from mitigation.
- **PR or release:** DeepSeek Explore for exact-head diff and CI, GLM General for substantive review. Keep merge and deploy with the root.
- **UI work:** GLM General for ordinary implementation, Kimi K3 for a larger independent workstream. The root verifies in the browser.
- **Long-running job:** background the command so the root stays available. Spawn DeepSeek only when something deterministic must be polled.

## Write the Assignment Contract

Pass only the context required to succeed:

1. One concrete objective and why it matters.
2. Exact scope.
3. Ownership boundary.
4. Constraints from the user, repository, and applicable skills.
5. Required validation and return format.
6. Read-only or authorized to mutate.
7. Leaf or explicitly authorized Kimi K3 coordinator. GLM and DeepSeek are always leaves.
8. The exact type and `opencode-go/...` id disclosed to the user.

Never send two agents overlapping implementation ownership in the same checkout.

## Coordinate and Integrate

- One precise worker is better than forced fan-out.
- Keep a useful path moving locally while agents work, unless the delegated operation is the whole task.
- Treat every result as evidence. Reopen live surfaces before claiming merged, deployed, or fixed.
- Synthesize disagreements. State which evidence wins.
- Preserve user approval for merge, deploy, production, and financial actions.

Return one integrated answer. Do not dump parallel reports on the user.
