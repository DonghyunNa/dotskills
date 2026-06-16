# Contributing

This is a personal repo, but issues and pull requests are welcome.

## Adding a skill

1. Create `skills/<name>/SKILL.md` with at least `name:` and `description:` frontmatter — `name` must match the directory.
2. If the skill ships scripts, put them in the same directory and mark them executable.
3. Update the catalog table in `README.md`.
4. Add an entry under `[Unreleased]` in `CHANGELOG.md`.

## Adding an agent

1. Create `agents/<name>.md` with frontmatter and prompt body.
2. Same catalog + changelog steps as above.

## Style

- Keep skill descriptions short and trigger-focused — Claude Code uses them for skill matching.
- Use kebab-case for filenames and directory names.
- Prefer English for SKILL.md frontmatter; the body can be in any language.

## License

By contributing, you agree that your contribution is licensed under the [MIT License](LICENSE).
