# dotskills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A personal, public collection of [Claude Code](https://claude.com/claude-code) skills and agents.

Treat this repo the way you'd treat `dotfiles` — a portable, versioned home for the workflow extensions I actually use day-to-day.

## Catalog

| Name | Type | What it does | How it triggers |
|---|---|---|---|
| [task-breakdown](skills/task-breakdown/SKILL.md) | skill | Decompose a fuzzy workflow into phases of small, verifiable atomic tasks with explicit dependencies and open questions | "업무 쪼개줘", "작업 분해", "task breakdown", "WBS", "어디서부터 시작하지" |
| [dev-clarify-requirements](skills/dev-clarify-requirements/SKILL.md) | skill | Clarify a fuzzy dev request along 5 axes (scope · motivation · constraints · success criteria · risk tolerance) | "요구사항 정리", "범위 확정", "scope this work", "dev requirements" |
| [repo-diagnose](skills/repo-diagnose/SKILL.md) | skill | One-page codebase health report across 7 axes — hotspots, coverage, coupling, integrations, known issues, build cycle, domain boundaries | "이 repo 진단", "codebase 분석", "온보딩 진단", "기술부채 어디" |
| [decision-compare-options](skills/decision-compare-options/SKILL.md) | skill | Compare 2+ options on a shared 5-axis matrix (impact · time · risk · dependencies · reversibility) and emit a recommendation with conditional alternatives | "옵션 비교", "tradeoff 정리", "decision matrix", "이거 어느 게 나을까" |
| [change-plan-rollback](skills/change-plan-rollback/SKILL.md) | skill | Define 3–5 measurable rollback signals + response actions + escalation paths for a planned production change | "롤백 계획", "abort criteria", "rollback signals", "kill switch 기준" |
| [dev-modernize-legacy](skills/dev-modernize-legacy/SKILL.md) | skill | Orchestrate the above sub-skills into a six-section legacy modernization strategy; this skill itself owns the strategy catalog (Strangler Fig, Branch by Abstraction, Characterization Tests, Mikado, Golden Master, Partial Rewrite, Big Bang, Leave Alone) | "레거시 개선 전략", "modernize legacy", "기술부채 우선순위", "strangler 패턴 적용", "rewrite vs refactor" |

> Skills and agents are added one at a time as they're vetted for public release. See [CONTRIBUTING.md](CONTRIBUTING.md).

## Layout

```
skills/   ↔  ~/.claude/skills/
agents/   ↔  ~/.claude/agents/
```

Each `skills/<name>/` is a self-contained directory containing at minimum a `SKILL.md`. Each `agents/<name>.md` is a single agent definition.

## Naming

ASCII lowercase **kebab-case**, 1–3 words (prefer 2). The directory name and the `name:` in `SKILL.md` frontmatter must match exactly.

Hyphen `-` is the only separator. `/` would force nested directories that Claude Code doesn't auto-discover, and `:` is reserved for plugin/marketplace namespacing (`harness-eval:full`).

Four allowed patterns:

| # | Pattern | When | Examples |
|---|---|---|---|
| 1 | `<verb>-<object>` | Single-action skill (default) | `register-skill`, `update-config`, `humanize-korean` |
| 2 | `<domain>-<verb>` | Domain-led name where the verb captures the whole action | `task-breakdown`, `court-finder`, `court-verify` |
| 3 | `<verb>` alone | Universal verb whose meaning is obvious from context | `verify`, `run`, `init`, `review` — avoid for new skills, collision risk |
| 4 | `<domain>-<verb>-<object>` | Use when it makes intent clearer than a 2-token name would; no count threshold | `court-find-venues`, `pr-review-diff`, `text-humanize-korean` |

When using pattern 4, the first token is always the **domain** — so domain-mates sort together and share a visible prefix.

### Don't

- Meta words in the name: `skill`, `tool`, `helper`, `agent` (the file is already a skill)
- Abstract nouns alone: `workflow`, `productivity` — say what you do
- Standalone abbreviations: `wbs`, `iac` — make an alias skill instead (`omw` ↔ `oh-my-wiki`)
- 4+ tokens — split into two skills or drop a word
- Korean / non-ASCII characters in the name — describe in Korean inside `description`, but the name string must be ASCII

### Description format

`SKILL.md` frontmatter `description:` follows three parts so triggering stays accurate:

1. One sentence — what the skill does (present tense, active voice)
2. **사용한다** — trigger phrases (Korean and/or English)
3. **사용하지 않는다** — 1–2 anti-examples so the LLM doesn't fire on adjacent requests

Trigger precision relies on part 3 — skip it and the skill ends up invoked on neighboring requests it doesn't actually serve.

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
