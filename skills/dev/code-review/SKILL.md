---
name: code-review
description: Effective code review — catch bugs, share knowledge, improve code quality
category: dev
tags: [review, quality, collaboration]
---

# Code review

A structured approach to reviewing code that finds real issues and spreads knowledge.

## When to use

- Any PR merge request
- Your own code before submitting (self-review)
- Reviewing architecture decisions

## Steps

### Before the review

1. **Read the PR description** — what's the intent? What's the scope?
2. **Checkout and run the branch locally** if the change is non-trivial
3. **Skim the full diff first** — get the shape of the change before diving into details

### During the review

1. **Check correctness first** — does the code do what it says?
2. **Check edge cases** — null inputs, empty lists, concurrent access, off-by-one
3. **Check error handling** — are failures handled? silent? logged? propagated?
4. **Check naming** — do variable/function names match what they do?
5. **Check tests** — do they cover the happy path? failure paths? edge cases?

### Comment categories

Use these prefixes in review comments:

- **[bug]** — correctness issue, will cause failures
- **[security]** — security concern (injection, auth, data leak)
- **[perf]** — performance issue
- **[style]** — naming, formatting, consistency
- **[question]** — genuine question, not a request
- **[nit]** — minor, optional suggestion

### After the review

1. **Summarize** — "overall looks good, 2 bugs and a security concern"
2. **Distinguish blocking from optional** — be explicit about what must change
3. **Approve when ready** — don't gatekeep on nits

## Pitfalls

- **Bikeshedding** — spending 20 comments on naming and missing the logic bug
- **Style-only reviews** — CI should handle formatting, humans should catch logic bugs
- **Reviewing too late** — review ASAP, not 3 days after the PR
- **Vague feedback** — "this doesn't look right" is useless. Say what's wrong and why.
- **Approving without reading** — if you're rubber-stamping, you're not reviewing

## Verification

- ✅ You understand what the PR changes and why
- ✅ You've flagged any correctness or security issues
- ✅ Your comments are specific, actionable, and categorized
- ✅ You've confirmed tests cover the new behavior