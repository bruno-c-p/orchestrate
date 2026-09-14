---
name: orchestrate-grok
description: Coordinate Grok sub-agents for substantial multi-step work. Requires Grok Build spawn_subagent (explore, plan, general-purpose), and uses workflow, background commands, and monitor when those fit. Use proactively when a request involves two or more separable workstreams; repository exploration plus implementation or verification; production diagnosis across logs, code, and live systems; PR or release review; multi-source analytics or research; or long-running tests, transfers, workflows, and monitors that should not block user communication. Assign read-only leaves to explore, architecture leaves to plan, and mutating or high-stakes leaves to general-purpose. Skip only trivial single-step or tightly sequential tasks.
---

# Orchestrate

Keep the root agent responsible for decomposition, user communication, approvals, synthesis, and the final claim. Delegate outcomes, not vague help. The root is the only coordinator. Every child is a leaf with a self-contained prompt.

## Decide Whether to Delegate

Delegate only when at least one condition holds:

- Two or more workstreams can proceed independently.
- A slow command, transfer, test suite, monitor, or data pull would otherwise make the root unavailable.
- A bounded investigation can gather evidence while the root handles the main path.
- An independent review would materially reduce implementation, production, security, payment, or release risk.

Stay single-agent when the task is small, the next step depends on the immediately previous result, multiple agents would touch the same mutable files in one checkout, or delegation overhead is comparable to doing the work directly.

## Select the Worker

Choose by ambiguity, coupling, and consequence rather than task size alone. Omit `model` unless the user named `grok-4.5` or `grok-4.6`.

| Type | Use when | Isolation | Do not use for |
|---|---|---|---|
| `explore` | Bounded evidence gathering. Codebase search, docs, diffs, CI, logs, official pages, locating the relevant code. Cannot edit files. | `none` | Edits, mutating commands, user-facing decisions |
| `plan` | The assignment is the approach itself: architecture, competing designs, an implementation plan under ambiguity. Cannot edit files. | `none` | Execution, live incident commands, the final call |
| `general-purpose` | Scoped implementation, review that needs shell, a substantial independent workstream, or senior judgment that must inspect and possibly run things. | `none` when this worker has exclusive files in the shared checkout. `worktree` when another writer is live or the parent is also editing. | Talking to the user, approvals, merge, deploy, the final claim |

Default to `explore` for evidence. Use `general-purpose` for implementation and for reviews that must run tools. Use `plan` when the deliverable is the approach.

A child cannot spawn. If a workstream needs further split, the child returns a split proposal and the root spawns. Prefer `workflow` with `parallel()` when the work-list is already known and each item is the same kind of job. Prefer a background command plus `monitor` when the job is a long process, not a judgment.

## Disclose Every Spawn

Immediately before every `spawn_subagent` call, send a concise commentary update that tells the user:

- The agent task name (`description`).
- The exact `subagent_type`.
- Isolation (`none` or `worktree`).
- The assigned role, always a leaf.
- The model slug only if the user requested one and it will be passed.

Group simultaneous spawns into one compact update when useful. For example:

```text
Spawning:
- `evidence_scout` — explore, isolation none — read-only leaf
- `impl_auth` — general-purpose, isolation worktree — implementation leaf
- `senior_critic` — plan, isolation none — architecture leaf
```

Describe these as requested configuration, not verified runtime identity. If the runtime later reports a different type, isolation, or model, disclose the correction.

## Use These Reusable Personas

Treat these as assignment shapes, not permanent project agents. Encode the shape in the prompt.

### Evidence scout — explore

Gather a defined evidence set and return paths, commands, timestamps, exact failures, and confidence. Good for service health, log excerpts, GitHub checks, current diffs, official documentation, and locating relevant code.

### Workflow operator — background command and monitor

Run and watch an already-defined, user-authorized job with explicit inputs and completion checks. Good for long transfers, test suites, CI watching, deterministic exports, or a running server. Do not invent scope or make approval decisions. Use a `workflow` fan-out instead when the job is many identical agent leaves over a known list.

### Implementation or review leaf — general-purpose

Own one bounded code surface or review target, complete the requested work, run targeted checks, and report changed files, evidence, residual risk, and a worktree path when isolated. Give distinct file or component ownership.

### Workstream owner — general-purpose

Own a substantial independent workstream as a leaf. Return one integrated result for that workstream. If more workers are required, return a split proposal rather than trying to coordinate children.

### Senior critic — plan, or general-purpose when the brief needs shell

Challenge architecture, diagnosis, security, financial correctness, or release readiness. Ask for the strongest competing explanation and the evidence that would distinguish it. Return a recommendation. Keep the final decision with the root and user.

## Apply Common Delegation Patterns

- **Production incident:** `explore` for live health and logs, `general-purpose` for the relevant code path. Have the root separate underlying cause from resilience mitigation.
- **PR or release:** `explore` for exact-head diff, checks, and CI; `general-purpose` for substantive review. Keep approval, merge, deploy, and final live verification with the root unless the user explicitly delegates those actions.
- **Analytics or payments:** separate `explore` scouts for independent sources, then `general-purpose` to reconcile definitions and mismatches. Keep money-sensitive calls with the root.
- **UI work:** `general-purpose` for bounded implementation. Use `worktree` if another writer is live. The root verifies in the browser before claiming the UI is done.
- **Long-running job:** background the command and attach `monitor` so the root stays available for corrections and approvals. Spawn a leaf only when the running job needs judgment, not when it needs a watcher.
- **Consulting or research:** `explore` for source collection, `general-purpose` for evidence normalization, `plan` or the root for the recommendation and tradeoffs.

## Write the Assignment Contract

Pass only the context required to succeed. Default to a self-contained prompt:

1. One concrete objective and why it matters.
2. Exact scope: repository, paths, systems, date range, or source set.
3. Ownership boundary, including files or surfaces the worker may change.
4. Constraints from the user, repository, and applicable skills.
5. Required validation and the expected return format, including a split proposal if the work needs more workers.
6. Whether the task is read-only or authorized to mutate state.
7. That the worker is a leaf.
8. The exact `subagent_type` and isolation that were disclosed to the user.

Use `resume_from` only when the next prompt genuinely depends on that child's transcript. Use `cwd` when the work lives in another directory. Never send two agents overlapping implementation ownership in the same checkout; give the second writer a `worktree`. Worktree edits stay in the child's tree until they are applied to the parent. The root does not claim the parent has those edits until that apply happens.

## Coordinate and Integrate

- Spawn only agents that have independent work. One precise worker is better than forced fan-out.
- Keep at least one useful path moving locally while agents work, unless the delegated operation is the whole task.
- Treat every result as evidence. Reopen authoritative live surfaces before claiming merged, deployed, fixed, paid, published, or delivered status.
- Follow up with `resume_from` when the next task depends on that child's context; spawn a new one when independence is more valuable.
- Stop or redirect with `kill_command_or_subagent` when ownership overlaps, assumptions diverge, or a worker expands scope.
- Synthesize disagreements explicitly. State which evidence wins and why.
- Preserve user approval boundaries for destructive, production, financial, publishing, messaging, merge, and deployment actions.

Return one integrated answer. Do not dump parallel reports on the user.
