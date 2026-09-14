---
name: orchestrate-claude
description: Coordinate Claude Code sub-agents for substantial multi-step work. Requires the Agent tool (Task is the old name) and the opus, sonnet, and haiku model aliases. Use proactively when a request involves two or more separable workstreams; repository exploration plus implementation or verification; production diagnosis across logs, code, and live systems; PR or release review; multi-source analytics or research; or long-running tests, transfers, workflows, and monitors that should not block user communication. Assign bounded leaf work to sonnet, mechanical work to haiku, and collaborative or high-stakes work to opus. Skip only trivial single-step or tightly sequential tasks.
---

# Orchestrate

Keep the root agent responsible for decomposition, user communication, approvals, synthesis, and the final claim. Delegate outcomes, not vague help.

## Decide Whether to Delegate

Delegate only when at least one condition holds:

- Two or more workstreams can proceed independently.
- A slow command, transfer, test suite, monitor, or data pull would otherwise make the root unavailable.
- A bounded investigation can gather evidence while the root handles the main path.
- An independent review would materially reduce implementation, production, security, payment, or release risk.

Stay single-agent when the task is small, the next step depends on the immediately previous result, multiple agents would touch the same mutable files in one checkout, or delegation overhead is comparable to doing the work directly.

## Select the Model

Choose by ambiguity, coupling, and consequence rather than task size alone. Always pass a `model` alias on the `Agent` call. Do not inherit. A parent on Opus would otherwise put Explore on Opus.

Use the aliases `opus`, `sonnet`, and `haiku`. They resolve to the current model in that family. Pass a full id such as `claude-opus-5` only when the user named that id.

| Alias | Use when | Typical effort | Do not use for |
|---|---|---|---|
| `sonnet` | The assignment can be completed as a bounded leaf. Evidence gathering, CI inspection, docs, scoped implementation, review, browser verification, multi-source reconciliation. | `xhigh` by default; `max` when quality matters more than latency or a weak result would cause rework. | Final high-stakes judgment or coordination of other agents. |
| `opus` | The assignment benefits from a stronger collaborative peer, a higher coding ceiling than Sonnet, or delegated coordination of its own independent workstream. Also independent senior judgment under ambiguity or high consequence: architecture, competing incident hypotheses, security or payment correctness, release-risk analysis, or an adversarial audit of a proposed plan. | `max` by default for peer coordination and higher-ceiling coding. `high` for senior judgment; `xhigh` for the hardest quality-first judgment. | Routine leaf work that Sonnet xhigh or max can complete. |
| `haiku` | Completely mechanical work whose result is deterministic and cheaply verified. File search, log grep, waiting on CI, a test run, a copy or export. | `low` or `medium`. | Ambiguous work, implementation, review, or anything a wrong answer would cause rework. |

Default to Sonnet xhigh for leaf work. Escalate to Sonnet max for difficult leaves. Use Haiku only as an explicit latency optimization for mechanical work. Move to Opus max when peer coordination, its higher ceiling, or lower wall-clock time than a long Sonnet max run justifies the cost. Move to Opus high when the assignment must resolve the central ambiguity or make a high-consequence judgment. Use Opus xhigh for the hardest quality-first work of that kind.

Pair the alias with a built-in type:

- `Explore` for read-only evidence. It cannot edit. Pass `sonnet` or `haiku`; never leave the model off.
- `general-purpose` for implementation, reviews that need shell, workstream ownership, and senior judgment that must run tools.
- `Plan` only when the deliverable is codebase research for an approach, still with an explicit model.

Use Sonnet and Haiku only as leaves and tell them not to spawn. Opus `general-purpose` may coordinate further only when the root explicitly assigns coordination ownership, the child work is independently scoped, and depth remains. Otherwise make it a leaf too. Omit `Agent` from a leaf's tools when you control the definition. When you spawn a built-in type, put the leaf/coordinator rule in the prompt.

## Disclose Every Spawn

Immediately before every `Agent` call, send a concise commentary update that tells the user:

- The agent task name.
- The exact `subagent_type`.
- The exact requested model alias, such as `sonnet` or `opus`.
- The exact requested effort, such as `xhigh` or `max`.
- Isolation (`worktree` or shared checkout).
- The assigned role and whether the agent is a leaf or an authorized coordinator.

Group simultaneous spawns into one compact update when useful. For example:

```text
Spawning:
- `evidence_scout` — Explore, sonnet, effort xhigh, shared checkout — read-only leaf
- `senior_critic` — general-purpose, opus, effort xhigh, shared checkout — senior-review leaf
```

Describe these as requested configuration, not verified runtime identity. If the runtime later reports a different type, model, or effort, disclose the correction. Prefer a self-contained prompt. When a resume or conversation fork requires inherited values, say they are inherited and name the parent's exact model and effort when known; if the runtime does not expose them, state that explicitly and never guess.

## Use These Reusable Personas

Treat these as assignment shapes, not permanent project agents.

### Evidence scout — Explore, sonnet xhigh

Gather a defined evidence set and return paths, commands, timestamps, exact failures, and confidence. Keep it read-only. Good for service health, log excerpts, GitHub checks, current diffs, official documentation, and locating relevant code. Use Explore, haiku, for a mechanical lookup whose answer is a path or a grep hit.

### Workflow operator — haiku, or a background command plus Monitor

Run and watch an already-defined, user-authorized job with explicit inputs and completion checks. Good for long transfers, test suites, CI watching, deterministic exports, or a running server. Do not invent scope or make approval decisions. Spawn a sonnet leaf only when the running job needs judgment.

### Implementation or review leaf — general-purpose, sonnet max

Own one bounded code surface or review target, complete the requested work, run targeted checks, and report changed files, evidence, and residual risk. Give distinct file or component ownership. Use `isolation: worktree` when another writer is live.

### Collaborative workstream owner — general-purpose, opus max

Own a substantial independent workstream that benefits from a stronger coding peer or further decomposition. Coordinate children only when explicitly authorized; otherwise remain a leaf. Return one integrated result for the assigned workstream.

### Senior critic — general-purpose, opus high or xhigh

Challenge architecture, diagnosis, security, financial correctness, or release readiness. Ask for the strongest competing explanation and the evidence that would distinguish it. Return a recommendation; keep the final decision with the root and user.

## Apply Common Delegation Patterns

- **Production incident:** Explore, sonnet xhigh for live health and logs; general-purpose, sonnet max for the relevant code path; opus max only if the investigation becomes a substantial coordinated workstream. Have the root separate underlying cause from resilience mitigation.
- **PR or release:** Explore, sonnet xhigh for exact-head diff, checks, and CI; general-purpose, sonnet max for substantive review. Keep approval, merge, deploy, and final live verification with the root unless the user explicitly delegates those actions.
- **Analytics or payments:** separate Explore, sonnet scouts for independent sources, then general-purpose, sonnet max to reconcile definitions and mismatches; opus high or xhigh for money-sensitive ambiguity.
- **UI work:** general-purpose, sonnet xhigh for ordinary bounded implementation and sonnet max for difficult implementation or adaptive browser verification; opus max for a larger independent workstream that needs further decomposition. Use `isolation: worktree` if another writer is live. The root verifies in the browser before claiming the UI is done.
- **Long-running job:** background the command and attach Monitor so the root stays available. Use haiku only when an agent has to poll something deterministic.
- **Consulting or research:** Explore, sonnet xhigh for source collection; general-purpose, sonnet max for evidence normalization; opus high or the root for the recommendation and tradeoffs.

## Write the Assignment Contract

Pass only the context required to succeed. Default to a self-contained prompt:

1. One concrete objective and why it matters.
2. Exact scope: repository, paths, systems, date range, or source set.
3. Ownership boundary, including files or surfaces the worker may change.
4. Constraints from the user, repository, and applicable skills.
5. Required validation and the expected return format.
6. Whether the task is read-only or authorized to mutate state.
7. Whether the worker is a leaf or an explicitly authorized opus coordinator. Sonnet and Haiku are always leaves.
8. The exact requested model alias, effort, type, and isolation that were disclosed to the user.

Resume an existing agent when the next task depends on its context. Never send two agents overlapping implementation ownership in the same checkout; give the second writer `isolation: worktree`. Worktree edits stay in the child's tree until they are applied to the parent. The root does not claim the parent has those edits until that apply happens.

## Coordinate and Integrate

- Spawn only agents that have independent work. One precise worker is better than forced fan-out.
- Keep at least one useful path moving locally while agents work, unless the delegated operation is the whole task.
- Treat every result as evidence. Reopen authoritative live surfaces before claiming merged, deployed, fixed, paid, published, or delivered status.
- Follow up with an existing agent when the next task depends on its context; spawn a new one when independence is more valuable.
- Stop or redirect work when ownership overlaps, assumptions diverge, or a worker expands scope.
- Synthesize disagreements explicitly. State which evidence wins and why.
- Preserve user approval boundaries for destructive, production, financial, publishing, messaging, merge, and deployment actions.

Return one integrated answer. Do not dump parallel reports on the user.
