# Scibly Skills

Open-source [Agent Skills](https://github.com/anthropics/skills) for instructional design and L&D (Learning & Development) work, built and used by [Scibly](https://github.com/scibly-dev). Each skill packages a proven instructional-design methodology — Bloom's Taxonomy, the ABCD model, didactic reduction, scenario design, and more — into step-by-step instructions an AI agent can follow.

## What's an Agent Skill?

An Agent Skill is a folder containing a `SKILL.md` file with YAML frontmatter (`name`, `description`) and Markdown instructions. Coding agents and assistants (Claude Code, Codex, Cursor, OpenCode, and others) can discover and load these skills to gain specialized, repeatable workflows — without you having to re-explain the methodology every time. See the [Agent Skills spec](https://github.com/anthropics/skills) for details.

## Skill catalog

| Skill | What it does |
|---|---|
| [`needs-analysis`](skills/needs-analysis/SKILL.md) | Synthesize SME transcripts, job descriptions, and performance data into a structured skill-gap analysis — figure out **what** to teach before building anything. |
| [`learning-objectives`](skills/learning-objectives/SKILL.md) | Turn vague topics or objectives into precise, observable, testable learning objectives using Bloom's Taxonomy and the ABCD model. |
| [`didactic-reduction`](skills/didactic-reduction/SKILL.md) | Strip a topic down to its learning-essential core using the three filters from German educational science (*Didaktische Reduktion*): exemplary, representative, transferable. |
| [`scenario-generation`](skills/scenario-generation/SKILL.md) | Generate realistic workplace scenarios, branching cases, and role-play briefs for practice and assessment. |
| [`assessment-design`](skills/assessment-design/SKILL.md) | Design quiz questions, knowledge checks, and assessments that go beyond recall, using Bloom's Taxonomy as a prompt lever for application-level items. |
| [`microlearning-design`](skills/microlearning-design/SKILL.md) | Design the *structure* of short, focused microlearning sessions (3–10 min) around one behavior change. |
| [`content-editing`](skills/content-editing/SKILL.md) | Review and improve AI-generated L&D content with a 5-fault-line editing framework — the "First-Draft Machine" approach. |
| [`instructional-prompt-engineering`](skills/instructional-prompt-engineering/SKILL.md) | Write better AI prompts for any L&D content using a 4-ingredient formula: Role, Audience, Bloom's level, Constraints, Task. |

### How the skills fit together

```
needs-analysis ──▶ learning-objectives ──▶ didactic-reduction
                                                  │
                  ┌───────────────────┬──────────┴──────────┐
                  ▼                   ▼                      ▼
        scenario-generation   assessment-design     microlearning-design
                  │                   │                      │
                  └───────────────────┴──────────┬───────────┘
                                                   ▼
                                           content-editing

instructional-prompt-engineering — used across every step above
```

You don't need to use them in order or all together — each skill works standalone, but they're designed to hand off to each other naturally (e.g. `learning-objectives` points to `assessment-design` and `microlearning-design` once objectives are defined).

## Installation

### Recommended: `npx skills`

Use [`npx skills`](https://github.com/vercel-labs/skills) to install skills into any supported agent (Claude Code, Codex, OpenCode, Cursor, and more):

```bash
# Install all skills into every agent detected on your machine
npx skills add scibly-dev/skills --all

# Install specific skills into specific agents
npx skills add scibly-dev/skills \
  --skill needs-analysis --skill learning-objectives \
  -a claude-code -a codex
```

### Claude Code (manual)

Copy or symlink a skill folder into your project's `.claude/skills/` directory, or into `~/.claude/skills/` for a global install:

```bash
cp -r skills/learning-objectives ~/.claude/skills/
```

### Codex / OpenCode / Cursor (manual)

Copy the skill folder into your project's `.agents/skills/` directory, or the tool's global skills directory (e.g. `~/.codex/skills/`, `~/.config/opencode/skills/`, `~/.cursor/skills/`).

### ChatGPT

ChatGPT doesn't have a native skill-folder mechanism. To use a skill there, open the relevant `SKILL.md`, copy everything **below the frontmatter**, and paste it into a Custom Instructions field or a Project's knowledge files. Include the skill's `description` too, so the assistant knows when to apply it.

### Other / generic agent harnesses

Any harness that scans a repository's `skills/` directory or reads `AGENTS.md` can use this repo directly — see [`AGENTS.md`](AGENTS.md).

## Repository structure

```
skills/
├── needs-analysis/SKILL.md
├── learning-objectives/SKILL.md
├── didactic-reduction/SKILL.md
├── scenario-generation/SKILL.md
├── assessment-design/SKILL.md
├── microlearning-design/SKILL.md
├── content-editing/SKILL.md
└── instructional-prompt-engineering/SKILL.md
```

Each skill is a single self-contained `SKILL.md` file in its own folder — one canonical copy per skill, installable into any supported agent.

## Contributing

- One skill per folder under `skills/<name>/`, containing a `SKILL.md` file.
- `name:` in the frontmatter must match the folder name exactly, kebab-case, no redundant prefixes (e.g. no `ai-` prefix — every skill here is an AI agent skill by definition).
- `description:` should be a rich, specific description of when to use the skill, including concrete trigger phrases — this is what the agent uses to decide when to load it.
- All skills and documentation are written in English.

## License

[MIT](LICENSE)
