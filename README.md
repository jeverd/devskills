# devskills

Open collection of skills, tutorials, and workflows for developers and AI practitioners.

Each skill is a standalone markdown file you can read, copy, or plug into [Hermes Agent](https://github.com/nousresearch/hermes-agent) as a reusable skill.

## What's inside

- **skills/** — reusable workflows and procedures (code review, debugging, deployment, etc.)
- **tutorials/** — step-by-step guides for tools, frameworks, and concepts
- **templates/** — starter templates for common setups

Skills are organized by category:

| Category | Description |
|---|---|
| `ai` | LLM ops, prompt engineering, RAG, fine-tuning |
| `dev` | General dev workflows, code quality, testing |
| `devops` | CI/CD, infrastructure, monitoring |
| `data` | Data pipelines, analysis, visualization |
| `security` | Appsec, compliance, hardening |

## Use a skill

**As a reference** — just read the markdown. Every skill is self-contained.

**With Hermes Agent** — copy the skill folder into `~/.hermes/skills/`:

```bash
# Example: install the debugging skill
cp -r skills/dev/debugging ~/.hermes/skills/
```

Or use the hermes CLI:

```bash
hermes skills add https://github.com/jeverd/devskills/raw/main/skills/dev/debugging/SKILL.md
```

## Contribute

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

tl;dr:

1. Fork the repo
2. Create your skill in the right category under `skills/`
3. Follow the [skill template](skills/_template/SKILL.md)
4. Submit a PR

## License

MIT — use however you want.