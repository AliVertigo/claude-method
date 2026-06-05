---
name: code-review
description: >
  Independent review of a code change (the current diff or a PR) for bugs, security, and
  quality — then fix the real findings. Triggers on "code review", "review this", "review
  the diff", "check this code", "review başlat". Reviewer-agnostic: uses a dedicated reviewer
  (e.g. Codex CLI) if the project has one, otherwise spawns review sub-agents or reviews
  critically itself.
---

# Code review

Review a diff with fresh, skeptical eyes and fix what's real. The goal is to catch what the
author's own pass missed — a second *independent* look, because one look always has a blind
spot.

## Scope
- Default to the change vs the base branch: `git diff main...HEAD` (or the PR diff).
- If there's no diff, stop and say so.

## How to run — strongest independent reviewer available, in order
1. A dedicated review tool the project has (e.g. Codex CLI: `codex review --base main "<prompt>"`).
2. Otherwise spawn 2-3 review sub-agents, each with a different lens.
3. Otherwise review the diff yourself — but adversarially, hunting for the author's mistakes.

## Lenses (each catches a different class)
- **Correctness:** null/undefined, error handling, edge cases (empty, huge, timeout), race
  conditions, off-by-one.
- **Security:** secret/PII exposure, injection (SQL/command/XSS), missing input validation,
  unsafe shell interpolation.
- **External contract:** does the code's assumption about an external system's data *shape*
  match reality? Mocked tests do NOT prove this (see the `method` skill's real-data gate).
- **Quality:** matches surrounding patterns, naming, dead code, DRY, function size.

## Findings → fix
- Score each finding by **severity**: Critical / High → fix (blocker); Low / Nit → log with
  a one-line reason, **no silent drops**.
- A reviewer (especially an AI one) can be wrong — if a finding looks like a false positive,
  verify before "fixing" it.
- After fixing, **re-review until clean** — two clean passes beats one.
