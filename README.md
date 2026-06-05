# method — a disciplined work loop for Claude Code (and humans)

A small, project-agnostic [Claude Code](https://claude.com/claude-code) skill bundle that
runs every non-trivial task through the same repeatable loop — so your AI agent (or you)
work the same careful way every time:

> **Understand → Plan → Build → Verify → Ship → Close**
> with premortem, code-review, **real-data validation**, and postmortem gates.

It's distilled from real project scar tissue. The failures it guards against are the ones
that actually bite: a feature that passes every test and review but is a **no-op against
real data**; processes that decay because they lean on willpower; stale docs; branch
collisions between concurrent agents.

## What's in the box

Four small, self-contained skills — `method` orchestrates the other three:

| Skill | What it does |
|---|---|
| **`method`** | The loop. Runs any plan-grade task through Understand → … → Close, calling the others at the right gates. Trivial work is exempt. |
| **`premortem`** | Stress-tests the plan *before* building: assume it failed, work backward, fix the blind spots. |
| **`code-review`** | Independent review of the diff (bugs / security / external-contract / quality), then fix by severity. **Codex-first** — asks once if Codex isn't installed, then remembers your choice. |
| **`postmortem`** | Blameless root-cause when something actually went wrong; a 2-line retro-note when it didn't. |

`premortem`, `code-review`, and `postmortem` work standalone too — useful on their own, not
only inside `method`.

## Why these rules

Most "process" is ignored or theatre. This keeps only the rules that earn their keep:

- **Real-data validation beats green tests.** If correctness depends on an external system's
  data *shape* (an API response, DB rows, a file format, a model's output), validate against
  the real thing. Mocks prove your *logic*, not your *assumptions*.
- **More AI-review passes ≠ more correctness.** Several reviews can share one blind spot.
- **Mechanical gates beat willpower.** Automate the check, or it decays.
- **Premortem before, retrospect after.** Cheap foresight up front; honest hindsight at end.
- **Severity-tier, never silent-drop.** Fix what matters; log the rest with a reason.
- **Don't fabricate.** Flag unknowns, cite evidence, say "I don't know."

## Install (Claude Code)

```bash
git clone https://github.com/AliVertigo/claude-method
# all skills, all your projects (global):
cp -r claude-method/skills/* ~/.claude/skills/
# …or into a single project:
cp -r claude-method/skills/* .claude/skills/
```

## Use

- Type **`/method`** to run the loop on your current task, **or** just say **"start the
  plan" / "new task" / "plana başla"** — it triggers automatically.
- `/premortem`, `/code-review`, `/postmortem` also work on their own.
- **Trivial work** (typo, one-line doc, single config value) is exempt.

A project-level skill overrides the global one, so `/method` is the same command everywhere —
generic by default, project-specific where a project ships its own.

## The method at a glance

Full loop in [`skills/method/SKILL.md`](./skills/method/SKILL.md). Each phase has a **gate**:

| Phase | Gate |
|---|---|
| **1. Understand** | Can state *what* / *why* / *which files* in one paragraph; no guessing |
| **2. Plan** | Premortem done; approach + scope clear; sign-off if risky |
| **3. Build** | Feature branch (isolated worktree if the repo is shared); review-clean |
| **4. Verify** | **Real-data validated** (or "N/A" stated); checks green; fix by severity |
| **5. Ship** | PR with write/read path + rollback; CI green → merge |
| **6. Close** | Retro (a note when clean, root-cause when not); state updated |

**Not a Claude Code user?** Every skill is a plain Markdown file — read them as process
guides. A human can run the same loop.

## License

MIT — see [LICENSE](./LICENSE). Use it, fork it, adapt it.
