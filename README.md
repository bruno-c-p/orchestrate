# Orchestrate

A skill for splitting substantial work across sub-agents.

The root agent keeps decomposition, user communication, approvals, synthesis, and the final claim. Workers get bounded assignments, not vague help.

The first implementation is for Codex (`spawn_agent`, Luna / Terra). Other providers will land in this repo.

## Install

```sh
npx skills add bruno-c-p/orchestrate
```

Inside Codex:

```text
$skill-installer https://github.com/bruno-c-p/orchestrate/tree/main/skills/orchestrate
```

Or copy the folder into a skills directory your agent already scans:

```sh
git clone https://github.com/bruno-c-p/orchestrate.git
cp -r orchestrate/skills/orchestrate ~/.agents/skills/
```

Codex also loads `~/.codex/skills/` and repo-local `.agents/skills/`.

## When to use it

Fire this skill when a request has two or more separable workstreams, mixes exploration with implementation or verification, or would otherwise pin the root agent on a long command, test suite, transfer, or monitor.

Stay single-agent for a small step, or for work that has to stay sequential.

## Layout

```text
skills/orchestrate/
  SKILL.md
  agents/openai.yaml
```

## License

MIT
