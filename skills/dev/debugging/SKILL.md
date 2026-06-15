---
name: systematic-debugging
description: 4-phase root cause debugging — understand bugs before fixing them
category: dev
tags: [debugging, root-cause, methodology]
---

# Systematic debugging

A structured approach to finding and fixing bugs. Works for any language or stack.

## When to use

- You hit a bug and don't know the cause
- You're tempted to just try random fixes
- The same type of bug keeps recurring
- You need to explain a bug to someone else

## Steps

### Phase 1: Reproduce reliably

1. **Write down the exact steps to trigger the bug** — don't skip this
2. **Minimize the repro** — strip away everything that doesn't matter
3. **Check if it's environment-specific** — does it happen locally? in staging? prod?
4. **Note the expected vs actual behavior** — be precise about both

```
Expected: POST /api/users returns 201 with user object
Actual:   POST /api/users returns 500 with null response
```

### Phase 2: Gather evidence

1. **Read the error message** — literally, word by word. 80% of bugs hide in the error text
2. **Check logs** — what happened right before the error?
3. **Add logging** — if logs don't show enough, add `console.log`/`print`/logging at decision points
4. **Inspect state** — what are the values of variables at the point of failure?
5. **Check recent changes** — `git log --oneline -10` or `git diff HEAD~5`

### Phase 3: Form a hypothesis

1. **Write down what you think is wrong** — "I think X because Y"
2. **Design the smallest test** that would confirm or deny your hypothesis
3. **Run the test** — if it doesn't confirm, form a new hypothesis
4. **Limit yourself to 3 attempts** — if you haven't found it, step back and gather more evidence

### Phase 4: Fix and verify

1. **Make the minimal fix** — don't refactor while fixing
2. **Verify the original repro case** now passes
3. **Check for side effects** — did the fix break anything else?
4. **Add a regression test** — prevent this class of bug from recurring
5. **Document the root cause** in the PR/commit message

## Pitfalls

- **Changing code before understanding the bug** — this creates new bugs and hides the original
- **Assuming the cause** — "it's probably a caching issue" is a hypothesis, not a fact
- **Ignoring flaky tests** — flaky means non-deterministic, which means there's a real bug
- **Fixing symptoms instead of causes** — adding a `try/catch` that swallows the error is not a fix
- **Not checking recent git history** — the person who last touched this code may have context

## Verification

- ✅ The original repro case no longer triggers the bug
- ✅ You can explain the root cause in one sentence
- ✅ A regression test exists that would catch this class of bug
- ✅ No other tests broke