# Orchestrate

The main agent answers you. It is also the one that says the work is done. Sub-agents get one job and a list of files they may touch. They do not merge or deploy. They do not pick up extra files.

Two skills live here. Same job, different tools.

- Codex: `skills/orchestrate-codex`. Needs `spawn_agent`, Luna, and Terra.
- Grok: `skills/orchestrate-grok`. Needs `spawn_subagent` (`explore`, `plan`, `general-purpose`). Uses `workflow`, background commands, and `monitor` when those fit.

## Install

```sh
npx skills add bruno-c-p/orchestrate
```

That installs both. Pass `--skill orchestrate-codex` or `--skill orchestrate-grok` to take one.

In Codex:

```text
$skill-installer https://github.com/bruno-c-p/orchestrate/tree/main/skills/orchestrate-codex
```

Or copy the folder your agent already scans:

```sh
git clone https://github.com/bruno-c-p/orchestrate.git
cp -r orchestrate/skills/orchestrate-codex ~/.agents/skills/
cp -r orchestrate/skills/orchestrate-grok ~/.grok/skills/
```

Grok also reads `~/.agents/skills/` and repo-local `.grok/skills/` or `.agents/skills/`.

## When to use it

Two chunks of work that do not share files. A test suite, transfer, or CI watch that would leave the main agent stuck for minutes. A review you should not rubber-stamp yourself.

A one-step fix does not need this. Neither does a sequence where step two is garbage without step one.

MIT
