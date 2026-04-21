# Karpathy-Inspired Codex Guidelines

Codex-native packaging for Andrej Karpathy's observations about LLM coding pitfalls.

This repo adapts the core behavioral guidance into OpenAI Codex-friendly building blocks:
- a concise always-on [AGENTS.md](AGENTS.md)
- a reusable skill in [.agents/skills/karpathy-guidelines/](.agents/skills/karpathy-guidelines/)
- focused reference examples for cross-language, JS/TS, Node, and React work

The goal is simple: help Codex make fewer wrong assumptions, avoid overengineering, keep diffs surgical, and work toward explicit success criteria.

## Why This Repo Exists

Karpathy's notes capture several common agent failure modes very well:
- silently making assumptions
- overcomplicating code and APIs
- editing unrelated code
- removing comments or code it does not fully understand
- claiming success without clear verification

This repo turns those ideas into a Codex-oriented setup you can drop into a repo or personal environment.

## Inspiration

This project was inspired by:
- Andrej Karpathy's post on LLM coding pitfalls and agent workflow: https://xcancel.com/karpathy/status/2015883857489522876
- Forrest Chang's Claude-oriented implementation: https://github.com/forrestchang/andrej-karpathy-skills

This repo is not affiliated with either source. It is a Codex-specific adaptation of the same core ideas.

## What Is Included

- `AGENTS.md`
  - concise always-on guidance for Codex sessions in this repo
- `.agents/skills/karpathy-guidelines/SKILL.md`
  - reusable workflow guidance for non-trivial coding tasks
- `.agents/skills/karpathy-guidelines/references/EXAMPLES.md`
  - cross-language behavior and workflow examples
- `.agents/skills/karpathy-guidelines/references/JS-TS-EXAMPLES.md`
  - JavaScript and TypeScript examples
- `.agents/skills/karpathy-guidelines/references/NODE-EXAMPLES.md`
  - Node and backend examples
- `.agents/skills/karpathy-guidelines/references/REACT-EXAMPLES.md`
  - React and UI examples

## What Is Different From The Claude Version

- `CLAUDE.md` is adapted into `AGENTS.md`
- the longer guidance lives in a Codex skill instead of one always-loaded file
- the examples are split into targeted reference files so Codex can load the right context when needed
- the language is tuned for Codex workflows such as minimal diffs, verification discipline, and success-criteria-driven execution

## Recommended Setup

For most repos, use both:

1. `AGENTS.md` at the repo root
2. `.agents/skills/karpathy-guidelines/` for reusable workflow guidance

That gives you a lightweight always-on policy plus a deeper skill Codex can invoke when the task is non-trivial.

## Installation

### Option A: Per-repo setup

Copy these into your repository:

- `AGENTS.md`
- `.agents/skills/karpathy-guidelines/`

### Option B: Global personal setup

Keep the AGENTS file and skill in your Codex home paths:

- `~/.codex/AGENTS.md`
- `~/.codex/skills/karpathy-guidelines/`

Use this when you want the same behavior across many repositories.

## Suggested Prompt Patterns

- "Use the karpathy-guidelines skill and fix this bug with a minimal diff."
- "Review this PR under the karpathy-guidelines skill."
- "Apply the repo AGENTS rules and implement the smallest working change."
- "Use success criteria first: keep a correct baseline, then optimize without changing behavior."

## How To Know It Is Helping

You should see:
- fewer unnecessary diffs
- fewer speculative abstractions
- more clarifying questions before risky changes
- better verification discipline
- smaller, easier-to-review PRs

## Suggested GitHub Metadata

Suggested repo description:

> Codex-native adaptation of Karpathy-inspired LLM coding guidelines, with AGENTS.md rules, Codex skills, and focused reference examples.

Suggested topics:

- `openai-codex`
- `codex`
- `agents-md`
- `ai-coding`
- `prompt-engineering`
- `developer-tools`

## License

MIT. See [LICENSE](LICENSE).

This repo was inspired by an MIT-licensed Claude-oriented adaptation and keeps explicit attribution to the original sources.
