---
name: building-agents
description: Patterns and pitfalls for building AI agent systems
category: ai
tags: [agents, llm, orchestration, tools]
---

# Building AI agents

Practical patterns for building agent systems that work reliably in production.

## When to use

- Building an autonomous or semi-autonomous AI agent
- Connecting LLMs to tools, APIs, or external systems
- Orchestrating multi-step AI workflows

## Steps

### 1. Start with a narrow, well-defined task

Don't build "a general assistant." Build "an agent that resolves customer support tickets under $50." Scope tightly, then expand.

### 2. Choose your architecture

**ReAct pattern** (reason → act → observe):
- Best for: tasks requiring external data or actions
- LLM decides what to do, uses a tool, observes result, decides next step
- Most common agent pattern

**Chain pattern** (step 1 → step 2 → step 3):
- Best for: predictable, linear workflows
- Each step is a separate LLM call with a specific job
- Easier to debug, less likely to go off-rails

**Router pattern** (classify → delegate):
- Best for: handling multiple distinct request types
- One LLM classifies the input, routes to a specialized handler
- Good for user-facing systems with diverse inputs

### 3. Define your tool interface carefully

Every tool should have:
- **Clear name** — `search_database`, not `query`
- **Explicit input schema** — types, constraints, required fields
- **Described output** — what shape does the response have?
- **Documented failure modes** — what happens on error? does it retry?

### 4. Add guardrails

```
- Max turns per task (prevent infinite loops)
- Token budget per turn
- Blacklisted actions (no deletions in production)
- Human-in-the-loop for high-stakes decisions
- Output validation (schema checks before proceeding)
```

### 5. Make it observable

Log every decision:
- What prompt was sent
- What tools were called, with what args
- What the LLM decided and why
- What went wrong (and where in the chain)

You will debug this. Make traces easy to read.

## Pitfalls

- **Giving too many tools** — 5 well-defined tools > 20 vague ones. LLMs hallucinate tool calls when the choice is ambiguous.
- **Trusting LLM output without validation** — always validate structured output. LLMs will return invalid JSON, skip required fields, or hallucinate enum values.
- **No max turns** — agents can loop forever. Always set a limit.
- **Vague tool descriptions** — "searches for things" → LLM uses it for everything. "Searches the product catalog by SKU, returns max 50 results" → LLM uses it correctly.
- **Ignoring latency** — each LLM call is 1-5 seconds. A 10-step agent = 10-50 seconds. Use caching, parallel calls, and shorter prompts where possible.

## Verification

- ✅ Agent completes its task in < max turns on 10+ test cases
- ✅ Tool calls match the described interface (no hallucinated args)
- ✅ Failure modes are handled (invalid input, API errors, timeouts)
- ✅ You can trace any agent run end-to-end from logs