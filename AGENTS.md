# AGENTS.md

Behavioral guidelines for Codex working in this repository, including both normal interactive sessions and the overnight single-agent automation system.

These rules bias toward caution, clarity, reviewability, and steady progress over speed.
For trivial tasks, use judgment and do not over-process simple changes.

## 1. Think Before Coding

**Do not assume. Do not hide confusion. Surface tradeoffs.**

Before implementing:

- State assumptions explicitly when they materially affect the solution.
- If multiple interpretations exist, present them instead of silently choosing one.
- If a simpler approach exists, say so.
- If something is materially unclear, stop and ask instead of guessing.
- If blocked by missing credentials, missing product decisions, missing APIs, or unclear requirements, stop and document the blocker instead of inventing an answer.

## 2. Simplicity First

**Write the minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No configurability that was not requested.
- No defensive code for scenarios that are impossible in the current system.
- Prefer existing project patterns over introducing new abstractions.
- Do not upgrade dependencies unless the task explicitly requires it.
- If 200 lines could be 50, rewrite it.

Ask yourself: **Would a senior engineer say this is overcomplicated?** If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Do not improve adjacent code, comments, or formatting unless required.
- Do not refactor things that are not broken.
- Match the existing style, even if you would normally write it differently.
- Do not change unrelated routes, components, modules, or features.
- If you notice unrelated dead code, mention it separately. Do not remove it unless asked.

When your changes create orphans:

- Remove imports, variables, functions, styles, or tests that your change made unused.
- Do not remove pre-existing dead code unless asked.

The test: **Every changed line should trace directly to the request.**

## 4. Goal-Driven Execution

**Define success criteria. Verify the result.**

Translate vague tasks into verifiable outcomes:

- "Add validation" → write tests for invalid inputs, then make them pass.
- "Fix the bug" → reproduce it with a test or clear verification path, then make it pass.
- "Refactor X" → ensure behavior is preserved before and after.

For multi-step tasks, state a short plan:

1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]

Strong success criteria let Codex work independently without drifting.

## 5. Repo Safety Rules

- Do not push to GitHub.
- Do not edit `.env*`, credentials, deployment secrets, SSH keys, tokens, browser cookies, or production infrastructure settings.
- Do not touch auth, payment, deployment, or other sensitive areas unless the task explicitly requires it.
- Prefer JS/TS over Python unless explicitly requested otherwise.
- Do not add dependencies without asking unless the task explicitly allows it.
- Prefer minimal diffs and backward compatibility.
- If no safe change is possible, leave the repo clean.

## 6. Verification Rules

- Run the narrowest relevant checks first, then broader checks only if needed.
- Use the project's existing scripts when available.
- For code changes, prefer some combination of build, typecheck, lint, focused tests, or manual reproduction.
- For UI changes, verify loading, empty, error, and success states when relevant.
- Never claim success without stating what was verified and what was not.
- When a quick change is requested, do not turn it into a redesign.

## 7. Overnight Worker Expectations

These rules apply especially during unattended or overnight runs:

- Work only on the current task.
- Keep changes small, reviewable, and directly tied to the task.
- Do not spend unlimited time on one task.
- If the task requires a large rewrite, stop and document the blocker.
- If the same class of error persists after a few reasonable attempts, stop and document the blocker.
- A completed overnight task should end in one local commit.
- If blocked, write a concise blocker note and stop cleanly.
- For overnight queue execution, use the `overnight-worker` skill.

## 8. Design Discipline

For UI and frontend work:

- Consult `DESIGN.md` and use the `ui-design-guardrails` skill.
- Keep the main user flow obvious.
- Prefer one clear primary CTA per screen.
- Reuse existing spacing, typography, and component patterns before inventing new ones.
- Avoid flashy or obviously AI-generated styling unless explicitly requested.
- Make sure the UI feels understandable within a few seconds of opening the screen.
- Prefer clean mobile and desktop usability over decorative polish.

## 9. How to Work Under These Rules

- Prefer high-confidence, minimal diffs.
- Verify with the narrowest relevant tests first, then broader checks if needed.
- Call out assumptions, risks, and follow-up work explicitly.
- When asked for a quick change, do not turn it into a redesign.
- For non-trivial code changes, use the `karpathy-guidelines` skill.
- For UI work, use the `ui-design-guardrails` skill.

## 10. These Guidelines Are Working If

- Diffs contain only requested changes.
- Codex asks clarifying questions before avoidable mistakes.
- Fixes stay simple on the first pass.
- PRs or commits are clean and easy to review.
- UI changes improve clarity without unnecessary visual noise.
- Overnight tasks end in clean commits, clean blockers, or a clean repo state.
