---
name: code-review
description: >
  Independent review of a code change (the current diff or a PR) for bugs, security, and
  quality — then fix the real findings. Triggers on "code review", "review this", "review
  the diff", "check this code", "review başlat". Prefers the Codex CLI for a strong
  independent review; if Codex isn't installed it asks ONCE, and never nags again if you
  opt out.
---

# Code review

Review a diff with fresh, skeptical eyes and fix what's real — a second INDEPENDENT look,
because one look (even the author's careful one) always has a blind spot.

## Scope
- Default to the change vs the base branch: `git diff main...HEAD` (or the PR diff).
- If there's no diff, stop and say so.

## Reviewer selection — Codex-first, ask-once

The preferred reviewer is the **Codex CLI**: a strong reviewer independent of the agent that
wrote the code. Resolve it like this, in order:

1. **Codex installed?** Run `which codex`.
   - **Yes** → use Codex (Step A).
2. **Codex NOT installed** → check the opt-out marker: `test -f ~/.claude/codex-optout`.
   - **Marker exists** → the user already declined Codex. Silently use the fallback
     reviewer (Step B). **Do NOT ask again.**
   - **No marker** → ask the user EXACTLY ONCE:
     > "Codex CLI isn't installed — it gives a stronger, independent review. Want to install
     > it, or review with built-in sub-agents instead? (Say *'never ask again'* to always
     > skip Codex.)"
     - **Install** → show the setup below, run it, then re-check `which codex`.
     - **Proceed without (this time)** → use Step B; you may ask again next time.
     - **"never ask again"** → write the marker so it's never asked again, then use Step B:
       ```bash
       mkdir -p ~/.claude && printf 'opted out of codex review on %s\n' "$(date +%F)" > ~/.claude/codex-optout
       ```

### Step A — Review with Codex
```bash
codex review --base main "$(cat <<'PROMPT'
Review this PR diff. Report each finding as:
[SEVERITY] file:line — what / why it's a problem / how to fix.
Cover: correctness (null/edge/race), security (secrets/injection/validation),
external-data-contract (does the code's assumption about an external system's data SHAPE
match reality?), and quality. SEVERITY = CRITICAL / HIGH / MEDIUM / LOW.
End with: total counts + verdict (APPROVED / APPROVED WITH NOTES / CHANGES REQUESTED).
PROMPT
)"
```
Codex reads its model/effort from `~/.codex/config.toml`. If Codex errors (auth/timeout),
show the error and fall back to Step B.

**Codex CLI setup** (only if the user chooses to install):
```bash
npm install -g @openai/codex
codex login
# optional tuning — set your model/effort:
mkdir -p ~/.codex && printf 'model_reasoning_effort = "high"\n' >> ~/.codex/config.toml
```

### Step B — Fallback review (no Codex)
Spawn 2-3 review sub-agents in parallel, each with a different lens (below). If sub-agents
aren't available, review the diff yourself — adversarially, hunting for the author's mistakes.

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
