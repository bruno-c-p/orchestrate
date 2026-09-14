# Orchestrate

The main agent answers you. It is also the one that says the work is done. Sub-agents get one job and a list of files they may touch. They do not merge or deploy. They do not pick up extra files.

Same job, different tools. Hard work on the expensive model. Busywork on the cheap one.

- Codex: `skills/orchestrate-codex`. `spawn_agent`. Luna for leaves, Terra for high-stakes.
- Grok: `skills/orchestrate-grok`. `spawn_subagent` (`explore`, `plan`, `general-purpose`).
- Claude: `skills/orchestrate-claude`. `Agent` tool. Opus / Sonnet / Haiku.
- Gemini CLI: `skills/orchestrate-gemini`. `gemini-3.8-flash` thinking high vs low.
- Antigravity: `skills/orchestrate-antigravity`. Same Flash split, via `invoke_subagent`.
- OpenCode Go: `skills/orchestrate-opencode`. Kimi K3 / GLM-5.3-Flash / DeepSeek V4.1 Flash.
- Cursor: `skills/orchestrate-cursor`. API frontier / included Grok / Composer 2.5.

## Install

```sh
npx skills add bruno-c-p/orchestrate
```

That installs all of them. Pass `--skill orchestrate-codex` (or grok, claude, gemini, antigravity, opencode, cursor) to take one.

In Codex:

```text
$skill-installer https://github.com/bruno-c-p/orchestrate/tree/main/skills/orchestrate-codex
```

Or copy the folder your agent already scans:

```sh
git clone https://github.com/bruno-c-p/orchestrate.git
cp -r orchestrate/skills/orchestrate-codex ~/.agents/skills/
cp -r orchestrate/skills/orchestrate-grok ~/.grok/skills/
cp -r orchestrate/skills/orchestrate-claude ~/.claude/skills/
cp -r orchestrate/skills/orchestrate-gemini ~/.gemini/skills/
cp -r orchestrate/skills/orchestrate-antigravity ~/.gemini/antigravity/skills/
cp -r orchestrate/skills/orchestrate-opencode ~/.config/opencode/skills/
cp -r orchestrate/skills/orchestrate-cursor ~/.cursor/skills/
```

## When to use it

Two chunks of work that do not share files. A test suite, transfer, or CI watch that would leave the main agent stuck for minutes. A review you should not rubber-stamp yourself.

A one-step fix does not need this. Neither does a sequence where step two is garbage without step one.

MIT
