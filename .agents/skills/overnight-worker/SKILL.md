---
name: overnight-worker
description: Use for task-based work inside the single-agent overnight Codex automation system. Apply when a task file defines goal, scope, definition of done, hard rules, and stop conditions, and the agent should complete exactly one task, verify it, write a summary or blocker, and stop cleanly.
---

# Overnight Worker

This skill is for the repo-side behavior of Codex when it is running in the single-agent overnight automation system.

It does **not** replace the runner script. The runner still handles queue order, timeouts, patch saving, reset-on-failure, and log capture. This skill only defines how Codex should behave **inside one task**.

## What this skill should optimize for
- Complete exactly one task
- Stay within the task's scope
- Keep changes small and reviewable
- Verify before claiming success
- Stop cleanly when blocked
- Avoid poisoning later tasks with broad or unstable edits

## Inputs this skill expects
Usually the runner prompt will include:
- global wrapper rules
- the current task file
- repo-local AGENTS.md
- repo-local DESIGN.md for UI work

The task file should define:
- Goal
- Scope
- Acceptance criteria
- Definition of done
- Hard rules
- Stop condition

## Required behavior

### 1. Work on only the current task
- Do not start another task
- Do not "sneak in" extra improvements
- Do not keep polishing after the task is done

### 2. Keep the change local
- Prefer targeted edits
- Avoid broad rewrites
- Avoid dependency upgrades unless explicitly required
- Match existing patterns

### 3. Use a short execution loop
Use this rough loop:
1. inspect relevant code and clarify the plan internally
2. make the smallest plausible change
3. run narrow relevant verification
4. iterate up to 3 repair cycles if needed
5. stop when acceptance criteria are satisfied or blocking conditions are met

### 4. Stop instead of guessing
Write a blocker and stop if:
- the task needs missing credentials, secrets, or API keys
- product behavior is ambiguous in a way that changes implementation
- the change clearly requires a broad rewrite outside scope
- the same class of failure remains after 3 fix/verify cycles
- the repo baseline is broken in a way that prevents trustworthy completion

### 5. Verification expectations
Use the smallest meaningful checks first:
- focused test
- build
- lint
- typecheck
- manual reproduction or targeted UI verification

Do not run giant expensive commands first unless they are the only meaningful verification.

### 6. Commit discipline
If the task is complete and the runner has not already handled it:
- create one local commit for the task
- keep the message short and task-oriented

If the task is blocked:
- do not create a speculative "partial progress" commit unless the task instructions explicitly request that

## Output files
Use these references for formatting:
- `references/summary-format.md`
- `references/blocker-format.md`

The runner may ask you to print final structured lines such as:
- `TASK_STATUS`
- `COMMIT_HASH`
- `SUMMARY_FILE`
- `BLOCKER_FILE`

If so, print them exactly as requested.

## Reference files
- `assets/task-template.md`
- `references/summary-format.md`
- `references/blocker-format.md`
- `references/failure-recovery.md`
