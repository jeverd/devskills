---
name: ci-pipelines
description: Set up reliable CI/CD pipelines that catch real bugs, not just lint errors
category: devops
tags: [ci, cd, pipelines, github-actions]
---

# CI/CD pipelines

Build pipelines that catch real issues and ship with confidence.

## When to use

- Setting up CI for a new project
- Improving an existing pipeline that's too slow or misses bugs
- Adding deployment automation

## Steps

### 1. Start with what matters

Pipeline priority order:

1. **Tests** — unit + integration, the core reason CI exists
2. **Lint + type check** — catches classes of bugs cheaply
3. **Security scan** — dependency vulnerabilities, secret detection
4. **Build** — confirms the thing actually compiles
5. **Deploy** — only after the above pass

### 2. Keep it fast

- **Fail fast** — run the checks most likely to fail first
- **Parallelize** — lint, type check, and tests can run simultaneously
- **Cache** — dependency installs, build artifacts
- **Skip wisely** — `ci-skip` labels for docs-only changes

Example GitHub Actions structure:

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm test

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level=high

  deploy:
    needs: [lint, test, security]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - run: npm run deploy
```

### 3. Make failures actionable

Every failure should tell you:
- **What broke** — which test, which check
- **Where** — file, line, commit
- **Why** — the actual error, not "process exited with code 1"

Tips:
- Use `--reporter` flags for better test output
- Set `CI=true` for better Node.js output
- Don't silent-fail with `|| true` — if a step can fail, let it

### 4. Secure the pipeline

- **No secrets in code** — use environment variables / secrets management
- **Pin action versions** — `actions/checkout@v4.1.1`, not `@main`
- **Limit permissions** — `permissions: contents: read` unless you need write
- **Scan dependencies** — `npm audit`, `pip audit`, `snyk test`

## Pitfalls

- **pipelines that only lint** — if CI only runs lint and format, it's not catching real bugs
- **50-minute pipelines** — developers will avoid CI. Target < 10 min for the critical path.
- **Flaky tests in CI** — they erode trust. Quarantine or fix flaky tests immediately.
- **Deploying on every merge without gates** — at minimum, require passing CI + 1 approval
- **Caching everything** — stale caches cause "works on CI, fails locally" (and vice versa). Be selective.

## Verification

- ✅ Pipeline runs in < 10 minutes
- ✅ Every common bug type (type error, test failure, security vuln) is caught
- ✅ Failures produce clear, actionable messages
- ✅ Secret values never appear in logs