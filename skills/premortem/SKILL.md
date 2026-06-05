---
name: premortem
description: >
  Stress-test a plan or decision by assuming it ALREADY failed 6 months from now and working
  backward to find why. Surfaces blind spots before you commit. Triggers on "premortem this",
  "what could kill this", "what am I missing", "stress-test this plan", "poke holes in this".
  Use on plans/launches/decisions where being wrong is costly — not on trivial questions.
---

# Premortem

Default thinking is optimistic; this deliberately breaks it. Don't look for why the plan
works — assume it's dead and write the autopsy.

## Frame
Say it out loud: *"It's 6 months from now. [The plan] has failed. We're looking back to
understand exactly what went wrong."*

## Steps
1. **Ground it.** Know what the plan is, who it affects, and what success would have been —
   you can't define failure without defining success. Read the real artifacts (code, docs,
   data) you're reasoning about; don't argue from imagination.
2. **List failure modes.** Generate the specific, genuine ways it died — each in 1-2
   sentences, grounded in the actual plan. Don't pad with generic risks; let the real risk
   set the count. For a high-stakes plan, run several **independent** investigators (real
   sub-agents or separate passes) so later ideas don't anchor on earlier ones.
3. **Verify each against evidence.** For every concrete claim (a number, a file, an API
   limit, a dependency), cite a real source or tag it `[unverified]`. A failure story you
   can't ground is theatre — mark it as something to verify, not a finding.
4. **For each real failure mode capture:** the underlying assumption being taken for granted,
   and 1-2 observable early-warning signs (a metric + threshold, not a vague feeling).

## Output
- **Most likely failure** (evidence-backed) and **most dangerous failure** (highest damage).
- **The single biggest hidden assumption** across all of them.
- **Revised plan:** concrete changes that kill the *confirmed* failure modes. Unverified
  risks get a *verify-this* action, NOT a fix — don't spend mitigation budget on imaginary
  risks.
- **Pre-launch checklist:** 3-5 things to test or verify before committing.

## Its own failure mode
A premortem fails by producing well-told *false* stories — invented specifics the user then
plans around. Write only claims that would survive someone pulling up the real code/data
later; tag the rest `[unverified]`.
