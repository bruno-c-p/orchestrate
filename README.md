# Orchestrate

The main agent answers you. It is also the one that says the work is done. Sub-agents get one job and a list of files they may touch. They do not merge or deploy. They do not pick up extra files.

Codex is the only agent hooked up so far. You need `spawn_agent`, Luna, and Terra. I will add others. They are not in here yet.

## Install

```sh
npx skills add bruno-c-p/orchestrate
```

In Codex:

```text
$skill-installer https://github.com/bruno-c-p/orchestrate/tree/main/skills/orchestrate
```

Or copy `skills/orchestrate` into `~/.agents/skills/`. Codex also reads `~/.codex/skills/` and `.agents/skills/` inside a repo.

## When to use it

Two chunks of work that do not share files. A test suite, transfer, or CI watch that would leave the main agent stuck for minutes. A review you should not rubber-stamp yourself.

A one-step fix does not need this. Neither does a sequence where step two is garbage without step one.

Instructions are in `skills/orchestrate/SKILL.md`. MIT.
