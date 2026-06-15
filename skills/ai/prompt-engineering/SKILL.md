---
name: prompt-engineering
description: Practical prompt patterns for getting consistent, useful output from LLMs
category: ai
tags: [prompts, llm, patterns]
---

# Prompt engineering

Proven patterns for writing prompts that produce reliable, useful LLM output.

## When to use

- You're getting inconsistent or low-quality LLM responses
- You're building an LLM-powered feature or workflow
- You need to calibrate a prompt for production use

## Steps

### 1. Start with constraints, not instructions

Bad:
```
Write a good function that sorts a list.
```

Good:
```
Write a Python function that:
- Takes a list of integers
- Returns a new sorted list (ascending)
- Handles empty lists by returning []
- Does NOT use built-in sort()
- Includes type hints
```

Constraints > vague quality adjectives. "Good" means nothing. "Returns [] for empty input" means something.

### 2. Use structured output formats

Instead of free-form responses, specify the format:

```
Respond in this exact format:
DECISION: [YES/NO]
CONFIDENCE: [0-100]
REASONING: [1-2 sentences]
```

This makes output parseable and reduces hallucination drift.

### 3. Provide examples (few-shot)

One example does more than ten instructions:

```
Classify the sentiment:

"Great product!" → POSITIVE
"Terrible experience" → NEGATIVE
"It was okay" → NEUTRAL

Now classify: "Love the new update!"
```

### 4. Chain prompts for complex tasks

Don't ask one prompt to do everything. Chain them:

```
Prompt 1: "List the key requirements from this spec"
Prompt 2: "Given these requirements: [output from 1], write the implementation"
Prompt 3: "Review this implementation for bugs: [output from 2]"
```

Each prompt has one job. The output is more reliable.

### 5. Use system prompts for persistent behavior

System prompts set the personality and constraints that apply to every response:

```
You are a senior Python developer. You:
- Always include type hints
- Prefer stdlib over third-party packages
- Write docstrings for public functions
- Flag potential race conditions explicitly
```

## Pitfalls

- **Asking for too much in one prompt** — each additional task reduces quality on all tasks
- **Saying "be careful" or "be accurate"** — these are vibes, not constraints. Specify what "careful" means.
- **Not testing edge cases** — try your prompt with unusual inputs, not just the happy path
- **Over-optimizing for one model** — prompts that work on GPT-4 may not work on Claude or Llama. Test your target model.
- **Ignoring temperature** — for structured output, use low temperature (0-0.2). For creative tasks, go higher (0.7-1.0).

## Verification

- ✅ Your prompt specifies output format, not just intent
- ✅ You've tested with at least 5 diverse inputs
- ✅ Edge cases (empty, very long, ambiguous) produce reasonable output
- ✅ You're using the fewest constraints needed — no bloat