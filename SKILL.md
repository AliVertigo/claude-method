---
name: method
description: >
  Runs a disciplined, repeatable workflow for any non-trivial coding task so every piece of
  work follows the same high-quality loop: Understand → Plan → Build → Verify → Ship →
  Close, with premortem + code-review + real-data validation + postmortem gates. Triggers
  on "/method" and on start-of-work phrases: "start the plan", "new task", "let's build
  this", "plana başla", "yeni iş başlat", "method başlat". Trivial work (typo, one-line
  doc, single config value) is exempt.
---

# Method — disciplined work loop (project-agnostic)

You are starting a piece of work. Run it through ONE repeatable loop so quality and process
are consistent every time. Each phase has a **gate**: don't advance until it's met.

## 0. Triage: trivial or plan-grade?
- **Trivial** (typo, one-line doc/comment, single config value, version bump, obvious
  one-liner) → say "trivial — skipping the loop", do it, verify, done.
- **Plan-grade** (new feature, logic/schema change, multi-file, touches an external
  contract, anything you'd want to think before doing) → run the full loop.

## The loop

### 1. Understand
- Restate the task in your own words. Read the relevant code — don't guess.
- List what you don't know (config values, names, external behavior); find it or ask —
  never fabricate. "I don't know" beats a confident wrong answer.
- **Gate:** you can state *what*, *why*, and *which files* are affected in one paragraph.

### 2. Plan (premortem the approach)
- Decide the approach + scope boundary. Keep it one small, shippable unit.
- **Premortem:** assume it's 6 months later and this failed — list the top ways it broke,
  then design against them. (Use a `/premortem` skill if available; else do it inline.)
- If it's risky or touches protected resources (prod config, secrets, DB, CI/workflows,
  infra), or scope is unclear → confirm with the user before building.
- **Gate:** approach clear, premortem done, user sign-off if needed.

### 3. Build
- Work on a **feature branch**, never directly on main.
- **If other agents/sessions may touch the same repo concurrently, use an isolated git
  worktree** (`git worktree add <path> -b <branch> origin/main`) — shared working trees
  cause branch/HEAD collisions.
- Implement matching the surrounding code's patterns and conventions.
- **Static review loop:** review the diff for bugs/security/quality (use a `/codex-review`
  or `/code-review` skill if available; else self-review critically, or spawn a review
  sub-agent) → fix → re-review until clean.
- **Gate:** change complete on the branch, review-clean.

### 4. Verify (claim ≠ proof)
- **Real-data validation — the most important gate.** If correctness depends on the actual
  *shape* of data from an external system (a third-party API response, DB rows, a file
  format, another service's or model's output), you MUST validate against the REAL thing: a
  live read-only call or a genuine sample. Mocked/hand-written fixtures prove your *logic*,
  NOT that your assumption about the external shape is correct. "All tests green" routinely
  ships a no-op when the real shape differs.
- Run the project's checks: tests, lint, build, type-check, CI-equivalent.
- Fix findings by **severity**: Critical/High → fix (merge blocker); Low/Nit → log with a
  one-line reason, **no silent drops**.
- **Gate:** real-data validated (or "N/A" stated explicitly) + checks green.

### 5. Ship
- Open a PR. In the description answer:
  - **Scope** + changed files + risk (low/med/high).
  - **Write/read path:** what data does this code produce, and who consumes it? (If nothing
    consumes it yet, label that explicitly and set a date to wire it up.)
  - **Verification** evidence + **rollback** (how to undo).
- CI green → squash-merge. (If the work is analysis/decision rather than code → deliver a
  clear written report instead of a PR.)
- **Gate:** merged + CI green, or report delivered.

### 6. Close
- **Retrospective:** did each step catch something the previous one missed? A clean run →
  a 2-line retro-note; something actually broke → a real root-cause (use a `/postmortem`
  skill if available). Fix anything it surfaces.
- Update project state/docs. Capture **stable** lessons (patterns, decisions) into the
  project's memory — not volatile state.
- **Gate:** state reflects reality; next step is clear.

## Principles (why this works)
- **Mechanical gates beat willpower.** Automate any check you can — self-discipline decays.
- **More AI-review passes ≠ more correctness.** Several reviews can share one blind spot;
  a single real-data check is worth more than another review pass.
- **Premortem before, retro after.** Cheap foresight up front; honest hindsight at the end.
- **Severity-tier, never silent-drop.** Fix what matters, log the rest with a reason.
- **Don't fabricate.** Flag unknowns, cite evidence, say "I don't know."

## Adapting to the project
- Honor the project's own rules first (its CLAUDE.md / contributing guide / conventions).
- Use whatever premortem/review/postmortem skills exist; fall back to inline reasoning.
- Scale ceremony to risk: a small library tweak needs less than a schema migration.
