# Contributing to devskills

Thanks for contributing! This guide covers how to add a skill, tutorial, or template.

## Skill format

Every skill lives in its own folder under `skills/<category>/` with a `SKILL.md` file.

```
skills/
  dev/
    debugging/
      SKILL.md          # required
      references/       # optional supporting docs
        common-errors.md
      scripts/          # optional scripts
        debug.sh
      templates/        # optional templates
        debug-config.yaml
```

### SKILL.md structure

Use this template:

```markdown
---
name: my-skill-name
description: One-line description of what this skill does
category: dev
tags: [list, of, relevant, tags]
---

# Skill name

Brief description of what this skill does and when to use it.

## When to use

- Scenario A
- Scenario B

## Steps

1. **Step one** — what to do
2. **Step two** — what to do next
3. **Step three** — final step

## Pitfalls

- Common mistake 1 and how to avoid it
- Common mistake 2 and what to watch for

## Verification

How to confirm it worked:
- Check X
- Run Y
```

### Guidelines

- **One skill per folder** — keep it focused and composable
- **Self-contained** — someone should be able to read just the SKILL.md and succeed
- **Concrete** — prefer specific commands and code over vague advice
- **Include pitfalls** — what goes wrong? what's non-obvious?
- **Test it** — verify your skill works before submitting

## Tutorials

Tutorials go in `tutorials/<category>/` as markdown files:

```
tutorials/
  ai/
    fine-tuning-llm.md
    building-rag-systems.md
```

Tutorial format:

```markdown
# Tutorial: [title]

## Prerequisites

What you need before starting.

## Overview

Brief summary of what you'll build/learn.

## Steps

1. Step one with code examples
2. Step two

## Next steps

Where to go from here.
```

## Categories

| ID | Use for |
|---|---|
| `ai` | LLMs, prompt engineering, RAG, fine-tuning, agents |
| `dev` | General development, code quality, testing, debugging |
| `devops` | CI/CD, infrastructure, monitoring, deployment |
| `data` | Data pipelines, analysis, SQL, visualization |
| `security` | Appsec, compliance, hardening, audits |

## PR process

1. Fork the repo
2. Create a branch: `git checkout -b add/my-skill-name`
3. Add your content
4. Verify it renders correctly
5. Open a PR with a description of what you're adding

## Code of conduct

Be respectful. No spam, no low-effort content, no plagiarized material.