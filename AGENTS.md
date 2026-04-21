# AGENTS.md

Behavioral guidelines for Codex to reduce common coding mistakes.
Merge these with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Do not assume. Do not hide confusion. Surface tradeoffs.**

Before implementing:

- State assumptions explicitly when they matter.
- If multiple interpretations exist, present them instead of silently choosing one.
- If a simpler approach exists, say so.
- If something is materially unclear, stop and ask instead of guessing.

## 2. Simplicity First

**Write the minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No configurability that was not requested.
- No defensive code for scenarios that are impossible in the current system.
- If 200 lines could be 50, rewrite it.

Ask yourself: **Would a senior engineer say this is overcomplicated?** If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Do not improve adjacent code, comments, or formatting unless required.
- Do not refactor things that are not broken.
- Match the existing style, even if you would normally write it differently.
- Do not rewrite or remove existing comments unless the task requires it or your change made them inaccurate.
- If you notice unrelated dead code, mention it separately. Do not remove it unless asked.

When your changes create orphans:

- Remove imports, variables, functions, or tests that your change made unused.
- Do not remove pre-existing dead code unless asked.

The test: **Every changed line should trace directly to the request.**

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Translate vague tasks into verifiable outcomes:

- "Add validation" → write tests for invalid inputs, then make them pass.
- "Fix the bug" → reproduce it with a test or clear verification path, then make it pass.
- "Refactor X" → ensure behavior is preserved before and after.
- "Optimize X" → keep or write a correct baseline first, then optimize while preserving checked behavior.

For multi-step tasks, state a short plan:

1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]

Strong success criteria let Codex work independently without drifting.
Prefer declarative success criteria over step-by-step micromanagement when possible.

## How to work under these rules

- Prefer high-confidence, minimal diffs.
- Verify with the narrowest relevant tests first, then broader checks if needed.
- Call out assumptions, risks, and follow-up work explicitly.
- When asked for a quick change, do not turn it into a redesign.

* prefer JS/TS over Python unless explicitly requested
* do not add dependencies without asking
* prefer minimal diffs and backward compatibility

## These guidelines are working if

- Diffs contain only requested changes.
- Codex asks clarifying questions before avoidable mistakes.
- Fixes stay simple on the first pass.
- PRs are clean and easy to review.
