# Tutorial: Getting started with PR stacks

A step-by-step walkthrough of breaking a feature into a stack of small, reviewable PRs using Graphite.

## Prerequisites

- Git installed and a GitHub account
- [Graphite CLI](https://graphite.com/docs/cli-quick-start) installed (`brew install graphite-cli` or `npm i -g @withgraphite/cli`)
- A repo you can push to

## The scenario

You're building a "user registration" feature. Instead of one giant PR with the model, endpoint, validation, and tests, you'll create a stack where each PR does one thing.

Your final stack will look like:

```
main
 └── 1. add-user-model (model + migration + model tests)
      └── 2. add-registration-endpoint (route + schema + endpoint tests)
           └── 3. add-input-validation (validation logic + validation tests)
```

## Step 1: Initialize Graphite

```bash
cd your-repo
gt init
# Select your trunk branch (usually main or master)
```

## Step 2: Plan your stack

Before writing code, decide how the work splits. Ask yourself:

- What's the foundation everything else depends on? → That's PR 1
- What can be reviewed independently? → Each of those is a PR
- Does each PR's title describe one thing? → If it has an "and", split it

For our feature:

1. **Add User model + migration** — foundation, everything depends on a User existing
2. **Add POST /users endpoint** — the route that calls the model
3. **Add input validation** — validation rules on top of the endpoint

Notice: each PR includes its own tests. We don't put tests in a separate PR.

## Step 3: Create the first branch

```bash
gt checkout --trunk
gt sync  # always sync before starting
gt create add-user-model -am "Add User model and migration"
```

Now implement the model and its tests on this branch:

```bash
# Write your model file and migration
# Write your model tests
git add -A
gt modify -am "Add User model and migration"
```

## Step 4: Stack the next branch

```bash
gt create add-registration-endpoint -am "Add POST /users registration endpoint"
```

This creates a new branch stacked on top of `add-user-model`. Implement the endpoint and its tests, then amend:

```bash
# Write your endpoint, schema, and tests
git add -A
gt modify -am "Add POST /users registration endpoint"
```

## Step 5: Stack one more

```bash
gt create add-input-validation -am "Add input validation for registration"
```

Implement validation and its tests:

```bash
# Write validation logic and tests
git add -A
gt modify -am "Add input validation for registration"
```

## Step 6: Review your stack

```bash
gt log
```

You should see:

```
◉ add-input-validation
│
◯ add-registration-endpoint
│
◯ add-user-model
│
◯ main
```

Each branch has one clear purpose. Navigate between them:

```bash
gt down      # go to add-registration-endpoint
gt down      # go to add-user-model
gt top       # jump back to add-input-validation
```

## Step 7: Submit the stack

```bash
gt submit --stack
```

This pushes all three branches to GitHub and creates three separate PRs, each targeting the one below it:

- PR #1: `add-user-model` → `main`
- PR #2: `add-registration-endpoint` → `add-user-model`
- PR #3: `add-input-validation` → `add-registration-endpoint`

Reviewers see small, focused diffs. PR #1 can be reviewed and merged first, and the others stay clean.

## Step 8: Address review feedback

If a reviewer comments on PR #2:

```bash
gt checkout add-registration-endpoint
# Make changes
gt modify -am "Add input validation to registration endpoint"
gt submit
```

Graphite auto-restacks PR #3 on top of the amended PR #2.

## Step 9: Merge and clean up

After PRs are approved and merged:

```bash
gt sync  # pulls trunk, rebases remaining branches, deletes merged branches
gt repo sync --restack  # restack any remaining open PRs
gt submit --stack  # push updated PRs
```

## What to remember

- **Each PR does one thing** — if the title has an "and", split it
- **Tests live with the code** — every PR includes its own tests
- **Always `gt sync` before starting** — stale trunk causes messy rebases
- **Use `gt modify` not `git commit`** — Graphite tracks the stack state
- **Submit the whole stack at once** — `gt submit --stack` (alias: `gt ss`)

## Next steps

- Read the [graphite-pr-stacks skill](../../skills/dev/graphite-pr-stacks/SKILL.md) for the full reference
- Check [Graphite's docs](https://graphite.com/docs) for advanced workflows
- Try creating a stack for your next feature