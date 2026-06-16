# Changelog

All notable changes to this repo are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Initial repo skeleton: README, LICENSE (MIT), CONTRIBUTING guide, `.gitignore`, GitHub Actions secret-scan workflow (gitleaks default rules), empty `skills/` and `agents/` directories.
- `task-breakdown` skill — decompose a fuzzy workflow into phases of small, verifiable atomic tasks with dependencies and open questions surfaced.
- Naming convention section in `README.md` — four allowed patterns, hyphen-only separator, three-part `description` format.
- `dev-modernize-legacy` skill — produce a legacy modernization strategy with diagnosis, curated options, risk-effect matrix, safety-net-first phased plan, and explicit rollback signals. Orchestrates `deep-interview`, `Explore` agents, and `task-breakdown`.
