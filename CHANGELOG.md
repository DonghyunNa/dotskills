# Changelog

All notable changes to this repo are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Initial repo skeleton: README, LICENSE (MIT), CONTRIBUTING guide, `.gitignore`, GitHub Actions secret-scan workflow (gitleaks default rules), empty `skills/` and `agents/` directories.
- `task-breakdown` skill — decompose a fuzzy workflow into phases of small, verifiable atomic tasks with dependencies and open questions surfaced.
- Naming convention section in `README.md` — four allowed patterns, hyphen-only separator, three-part `description` format.
- `dev-modernize-legacy` skill — produce a legacy modernization strategy with diagnosis, curated options, risk-effect matrix, safety-net-first phased plan, and explicit rollback signals.

### Changed

- `dev-modernize-legacy`: split into a thin `SKILL.md` entry point plus `references/{strategy-catalog,diagnosis-axes,plan-template}.md` (progressive disclosure pattern). Removed external dependencies — no longer references `deep-interview` or built-in subagents; the only inter-skill link is `task-breakdown` (same repo).
- `dev-modernize-legacy`: further refactored into a pure orchestrator. Each of the six phases (intake, diagnosis, options, evaluation, decomposition, rollback signals) now delegates to a dedicated sub-skill; the only domain logic left here is the strategy catalog. `references/diagnosis-axes.md` moved into the new `repo-diagnose` skill.
- Extracted four sub-skills with independent reuse value: `dev-clarify-requirements`, `repo-diagnose`, `decision-compare-options`, `change-plan-rollback`. Each can stand alone (e.g., `repo-diagnose` is useful for onboarding or audits independent of legacy planning).
