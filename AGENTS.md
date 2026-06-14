# AGENTS.md

This repository is a collection of standalone Agent Skills for instructional design and L&D work.

- Each skill lives in its own folder under `skills/<name>/`.
- Each skill folder contains a single `SKILL.md` file: YAML frontmatter (`name`, `description`) followed by Markdown instructions.
- `name` in the frontmatter matches the folder name.

To use a skill, copy or symlink its `skills/<name>/` folder into your agent's skills directory (e.g. `.claude/skills/`, `.agents/skills/`, `~/.codex/skills/`), or install with `npx skills add scibly-dev/skills`.

See [README.md](README.md) for the full skill catalog and per-tool installation instructions.
