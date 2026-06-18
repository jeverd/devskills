# devskills

Open collection of skills, tutorials, and workflows for developers and AI practitioners.

Each skill is a standalone markdown file you can read, copy, or plug into any AI agent framework (Claude Code, Codex, Cursor, Windsurf, Hermes, etc.).

## Skills

| Category | Skill | Description |
|----------|-------|-------------|
| dev | [html-share](skills/dev/html-share/SKILL.md) | Create standalone HTML pages and optionally share them via bore tunnel |
| dev | [graphite-pr-stacks](skills/dev/graphite-pr-stacks/SKILL.md) | Break tasks into small, reviewable PR stacks using Graphite |

> Want to add a skill? See [CONTRIBUTING.md](CONTRIBUTING.md) and the [skill template](skills/_template/SKILL.md).

## Tutorials

Coming soon — beginner-friendly walkthroughs based on skills in this repo.

## Use a skill

**As a reference** — just read the markdown. Every skill is self-contained.

**With any AI agent** — skills are standard markdown files. Drop them wherever your agent reads instructions:

```bash
# Claude Code — copy to your project's .claude/ or CLAUDE.md
cp skills/dev/debugging/SKILL.md .claude/skills/debugging.md

# Codex — add to your AGENTS.md or codex instructions
cat skills/ai/prompt-engineering/SKILL.md >> AGENTS.md

# Cursor — add to .cursor/rules/
cp skills/dev/code-review/SKILL.md .cursor/rules/code-review.mdc

# Windsurf — add to .windsurfrules
cat skills/ai/building-agents/SKILL.md >> .windsurfrules

# Hermes — copy to ~/.hermes/skills/
cp -r skills/dev/debugging ~/.hermes/skills/
```

The format is just structured markdown. Any agent that reads markdown instructions can use these.

## Contribute

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

tl;dr:

1. Fork the repo
2. Create your skill in the right category under `skills/`
3. Follow the [skill template](skills/_template/SKILL.md)
4. Submit a PR

## License

MIT — use however you want.