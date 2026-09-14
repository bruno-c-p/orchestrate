---
name: orchestrate-cursor
description: Coordinate Cursor Task sub-agents for substantial multi-step work. Requires the Task tool. Pin an API frontier model for high-effort work, included Grok (grok-4.6) for medium leaves, and composer-2.5 for cheap mechanical work. Use proactively when a request involves two or more separable workstreams; repository exploration plus implementation or verification; production diagnosis; PR or release review; multi-source research; or long-running jobs that should not block the root. Skip only trivial single-step or tightly sequential tasks.
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

Always pass an explicit `model` on the Task call. Do not inherit. Inherit is unreliable here and will put real work on Composer or an API model the user did not ask for.

| Model | Use when | Do not use for |
|---|---|---|
| API frontier (`claude-opus-5`, `gpt-5.6-sol`, or the API id the user named) | High-effort, ambiguous, or expensive-if-wrong work. Architecture, security, payments, a hard implementation, a review you should not rubber-stamp. | Grep, path lookup, waiting on CI, everyday scoped edits. |
| `grok-4.6` | Medium leaves on the included Cursor Models pool. Ordinary implementation, a normal review, evidence that needs judgment. | Final high-stakes calls. Mechanical busywork Composer can finish. |
| `composer-2.5` | Cheap mechanical work. File search, log grep, status polls, exports, a test run whose output is pass/fail. | Ambiguous work, implementation, review. Do not use the Fast variant unless the user asked for it. |

If the user named a specific API frontier id, use that id for high-effort spawns. If they did not, pass `claude-opus-5` or `gpt-5.6-sol` rather than included Grok. Grok stays on the medium band.

Default real leaves to `grok-4.6`. Escalate to an API frontier model when a weak result would cause rework. Use `composer-2.5` only for busywork.

Pair with a type when the session exposes one: Explore/search on Composer, general-purpose on Grok or frontier.

Tell Composer and Grok workers they are leaves. An API-frontier general-purpose worker may coordinate further only when the root explicitly assigns that.

## Disclose Every Spawn

Immediately before every Task call, tell the user:

- The agent task name.
- The type, if any.
- The exact model id.
- Leaf or authorized coordinator.
- Whether it is background.

Example:

```text
Spawning:
- `evidence_scout` — Explore, composer-2.5, background — read-only leaf
- `impl_auth` — general-purpose, grok-4.6 — implementation leaf
- `senior_critic` — general-purpose, claude-opus-5 — senior-review leaf
```

Describe these as requested configuration. If the runtime reports a different model, disclose the correction. Composer Fast and Grok Fast are not what was requested; say so if they show up.

## Use These Reusable Personas

Treat these as assignment shapes, not permanent project agents.

### Evidence scout — Composer 2.5

Gather a defined evidence set and return paths, commands, timestamps, exact failures, and confidence. Keep it read-only when the type allows.

### Workflow operator — Composer 2.5, or a background command

Run and watch an already-defined, user-authorized job. Do not invent scope or make approval decisions.

### Implementation or review leaf — grok-4.6

Own one bounded code surface or review target. Give distinct file ownership. Report changed files, evidence, and residual risk.

### Workstream owner or senior critic — API frontier

Own a substantial independent workstream, or challenge architecture, diagnosis, security, financial correctness, or release readiness. Coordinate children only when the root authorized it. Return one integrated result or a recommendation. Keep the final decision with the root and user.

## Apply Common Delegation Patterns

- **Production incident:** Composer for live health and logs, Grok for the relevant code path, API frontier only if the investigation becomes a coordinated workstream. The root separates cause from mitigation.
- **PR or release:** Composer for exact-head diff and CI, Grok for substantive review. Keep merge and deploy with the root.
- **UI work:** Grok for ordinary implementation, API frontier for a larger independent workstream. The root verifies in the browser.
- **Long-running job:** background the command so the root stays available. Spawn Composer only when something deterministic must be polled.

## Write the Assignment Contract

Pass only the context required to succeed:

1. One concrete objective and why it matters.
2. Exact scope.
3. Ownership boundary.
4. Constraints from the user, repository, and applicable skills.
5. Required validation and return format.
6. Read-only or authorized to mutate.
7. Leaf or explicitly authorized frontier coordinator. Composer and Grok are always leaves.
8. The exact model id disclosed to the user.

Never send two agents overlapping implementation ownership in the same checkout.

## Coordinate and Integrate

- One precise worker is better than forced fan-out.
- Keep a useful path moving locally while agents work, unless the delegated operation is the whole task.
- Treat every result as evidence. Reopen live surfaces before claiming merged, deployed, or fixed.
- Synthesize disagreements. State which evidence wins.
- Preserve user approval for merge, deploy, production, and financial actions.

Return one integrated answer. Do not dump parallel reports on the user.
