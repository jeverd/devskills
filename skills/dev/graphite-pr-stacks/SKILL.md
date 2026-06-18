---
name: graphite-pr-stacks
description: Break tasks into small, reviewable PR stacks using Graphite (gt CLI). Suggests task decomposition, gets confirmation, then creates the stack.
category: dev
tags: [graphite, pr, stack, review, git, workflow, decomposition]
---

# graphite-pr-stacks

Break any task into a stack of small, reviewable PRs using [Graphite](https://graphite.com). Each PR does one thing. This skill helps you plan the decomposition before you execute.

## When to use

- Starting a new feature, refactor, or bugfix that will touch multiple files or concerns
- Before writing code — use this skill to plan how the work splits into a PR stack
- When a task is too big for a single PR and needs to be broken down for review
- Any time you'd reach for `gt create` — this skill guides the decomposition first

## When NOT to use

- Trivial one-file changes — just open a single PR
- Urgent hotfixes that need to ship immediately — stack later

## Core principle

**Each PR does exactly one thing.** A reviewer should understand the full intent of a PR from its title alone. If a PR's title needs an "and" or a comma, it should be split.

Good PRs:
- "Add User model and migration"
- "Add POST /users endpoint"
- "Add user creation integration test"

Bad PRs:
- "Add users feature" (too broad — model + endpoint + tests + UI)
- "Add endpoint and fix lint" (two unrelated things)
- "Various fixes" (meaningless)

## Flow

```
describe task → propose decomposition → confirm → execute as PR stack
```

## Steps

### 1. Understand the task

Ask the user what they're building or changing. Get enough context to decompose:

- What's the end goal?
- What files or modules will be touched?
- Are there dependencies between the changes? (model before endpoint before test)

If the user already described the task, skip ahead.

### 2. Propose the decomposition

Break the task into ordered PRs. Each PR should:

1. **Be independently reviewable** — a reviewer can understand it without reading the other PRs
2. **Be small** — aim for < 300 lines changed. If you're over 500, split further
3. **Build on the previous PR** — this is what makes it a stack, not parallel branches
4. **Do one thing** — one concern, one layer, one responsibility

Present the decomposition as a numbered list:

```
PR stack for: "Add user authentication"

1. Add User model + migration
   - files: models/user.py, migrations/xxx_add_users.py
   - why first: everything else depends on the model existing

2. Add user registration endpoint (POST /users)
   - files: api/users.py, schemas/user.py
   - depends on: PR 1

3. Add login endpoint (POST /auth/login)
   - files: api/auth.py, services/auth.py
   - depends on: PR 1

4. Add auth middleware (JWT validation)
   - files: middleware/auth.py
   - depends on: PR 2, 3

5. Add integration tests for auth flow
   - files: tests/test_auth.py
   - depends on: PR 1-4
```

### 3. Get confirmation

Ask the user:

- Does the order make sense?
- Are the PRs the right size? Too granular? Too broad?
- Any PRs that should be merged or split?

Wait for explicit confirmation before proceeding. Adjust the decomposition based on feedback.

### 4. Create the PR stack

For each PR in order, from foundation to tip:

```bash
# Start from trunk
gt checkout --trunk
gt sync

# PR 1: foundation
gt create add-user-model -am "Add User model and migration"

# PR 2: builds on PR 1
gt create add-registration-endpoint -am "Add POST /users registration endpoint"

# PR 3: builds on PR 2
gt create add-login-endpoint -am "Add POST /auth/login endpoint"

# PR 4: builds on PR 3
gt create add-auth-middleware -am "Add JWT auth middleware"

# PR 5: builds on PR 4
gt create add-auth-tests -am "Add integration tests for auth flow"
```

Key rules:
- **Always start from trunk** — `gt checkout --trunk` first
- **Create in dependency order** — foundation PRs first, dependent PRs on top
- **Each `gt create` stacks on the current branch** — that's what builds the chain
- **Commit meaningful messages** — the `-m` flag becomes the default PR title
- **Stage your changes** — use `-a` to stage all, `-u` to stage tracked files only

### 5. Work on each PR

After creating the stack, implement each PR's changes:

```bash
# Navigate to a specific PR's branch
gt checkout add-user-model

# Make your changes, then amend or add commits
gt modify -a                    # amend current commit with all staged changes
gt modify -a -m "new message"   # amend with a new commit message
gt modify --commit -am "msg"    # add a second commit to this branch
```

If you edit a branch in the middle of the stack, Graphite auto-restacks all branches above it.

### 6. Submit the stack

```bash
# Submit the entire stack
gt submit --stack

# Alias
gt ss

# Submit as drafts first (recommended for review)
gt submit --stack --draft

# Submit with auto-merge when checks pass
gt submit --stack --merge-when-ready
```

### 7. Address review feedback

```bash
# Navigate to the PR branch that needs changes
gt checkout add-login-endpoint

# Make changes, then amend
gt modify -am "Add input validation to login endpoint"

# Re-submit updated PRs
gt submit
```

### 8. Sync and clean up after merge

```bash
# Pull trunk, rebase remaining open branches, delete merged branches
gt sync

# Restack any open PRs affected by the merge
gt repo sync --restack

# Re-submit restacked PRs
gt submit --stack
```

## Common decomposition patterns

### API endpoint

| # | PR | What it does |
|---|-----|-------------|
| 1 | Add model + migration | Database schema change |
| 2 | Add business logic | Service layer, validation, calculations |
| 3 | Add endpoint | Wire up the route, call service layer |
| 4 | Add tests | Unit + integration tests |

### Bug fix with regression test

| # | PR | What it does |
|---|-----|-------------|
| 1 | Add regression test | Test that demonstrates the bug (fails) |
| 2 | Fix the bug | Make the test pass |
| 3 | Add cleanup/refactor | Optional: improve surrounding code now that it's tested |

### Refactor

| # | PR | What it does |
|---|-----|-------------|
| 1 | Extract module/function | Move code without changing behavior |
| 2 | Update call sites | Point existing callers at the new module |
| 3 | Add new capability | Now build on the cleaner foundation |

### Feature with UI

| # | PR | What it does |
|---|-----|-------------|
| 1 | Add data model | Schema change |
| 2 | Add API endpoint | Backend API |
| 3 | Add UI component | Frontend display |
| 4 | Wire UI to API | Connect them |
| 5 | Add e2e test | End-to-end validation |

## Navigation cheat sheet

```bash
gt up              # Move one branch up the stack (toward tip)
gt down            # Move one branch down (toward trunk)
gt top             # Jump to the tip of the stack
gt bottom          # Jump to the bottom (closest to trunk)
gt checkout        # Interactive branch picker
gt log             # Visual stack graph
gt ls              # Short branch list
gt checkout --trunk  # Jump to trunk (main)
```

## MCP setup (for AI agents)

If you're using an AI agent like Claude Code or Cursor with Graphite's MCP:

```bash
# Claude Code
claude mcp add graphite gt mcp

# Cursor — add to .mcp.json or MCP settings
{
  "mcpServers": {
    "graphite": {
      "command": "gt",
      "args": ["mcp"]
    }
  }
}
```

Requires Graphite CLI v1.6.7+.

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `gt create` fails with "not initialized" | Run `gt init` in the repo root |
| Merge conflicts during restack | Resolve conflicts, then `gt continue` |
| Wrong branch order | Use `gt checkout` to navigate, `gt log` to see the stack |
| PR was merged but stack shows stale branch | `gt sync` — pulls trunk, rebases, deletes merged branches |
| Need to insert a PR mid-stack | Create it at the right position: `gt down` to the parent branch, then `gt create new-pr-name` |
| Need to reorder PRs | Not directly supported — best to delete and recreate (`gt delete <branch>`) |

## Pitfalls

- **Creating all PRs before writing any code** — `gt create` makes empty branches. Write code for PR 1, then create PR 2's branch, write code, etc. Creating the full stack upfront is fine for planning, but each PR needs real commits.
- **Making a PR too big** — if reviewers are skimming instead of reading, the PR is too big. Split it.
- **Parallel dependencies** — if PR 3 depends on PR 1 but not PR 2, you have two stacks, not one. Create them as separate stacks off trunk.
- **Forgetting to sync before starting** — always `gt sync` before creating new branches. Stale trunk = messy rebases later.
- **Using raw git commands** — `git rebase`, `git cherry-pick`, etc. conflict with Graphite's internal state. Use `gt modify`, `gt sync`, `gt restack` instead.
- **Skipping the decomposition step** — the whole point is thinking before coding. If you skip straight to `gt create`, you're not using this skill.

## Verification

After creating the stack:

```bash
gt log
```

You should see:
- All branches listed in dependency order
- Each branch has a meaningful name that matches its PR's single concern
- The bottom of the stack connects to trunk (main)

After submitting:

- All PRs appear on GitHub/Graphite
- Each PR's title is clear and self-contained
- Each PR's diff is < 300 lines (ideally < 200)
- Reviewers can understand each PR without reading the others