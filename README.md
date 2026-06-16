# dotskills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A personal, public collection of [Claude Code](https://claude.com/claude-code) skills and agents.

Treat this repo the way you'd treat `dotfiles` — a portable, versioned home for the workflow extensions I actually use day-to-day.

## Catalog

| Name | Type | What it does | How it triggers |
|---|---|---|---|
| [task-breakdown](skills/task-breakdown/SKILL.md) | skill | Decompose a fuzzy workflow into phases of small, verifiable atomic tasks with explicit dependencies and open questions | "업무 쪼개줘", "작업 분해", "task breakdown", "WBS", "어디서부터 시작하지" |

> Skills and agents are added one at a time as they're vetted for public release. See [CONTRIBUTING.md](CONTRIBUTING.md).

## Layout

```
skills/   ↔  ~/.claude/skills/
agents/   ↔  ~/.claude/agents/
```

Each `skills/<name>/` is a self-contained directory containing at minimum a `SKILL.md`. Each `agents/<name>.md` is a single agent definition.

## Install

Pick any skill (or agent) you want and symlink it into your global Claude directory:

```bash
# Clone once
git clone https://github.com/<your-user>/dotskills.git ~/code/dotskills

# Skill
ln -s ~/code/dotskills/skills/<name> ~/.claude/skills/<name>

# Agent
ln -s ~/code/dotskills/agents/<name>.md ~/.claude/agents/<name>.md
```

Symlinking (rather than copying) means `git pull` inside `dotskills/` is enough to update everything that's wired up.

## Contributing

Issues and PRs welcome. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE) — do whatever you like, no warranty.
