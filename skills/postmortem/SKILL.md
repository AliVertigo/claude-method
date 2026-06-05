---
name: postmortem
description: >
  Blameless incident postmortem — for something that actually went wrong (an outage, a bug
  that shipped, a broken deploy). Reconstructs the timeline, finds the root cause (not the
  symptom), and extracts durable, ideally mechanical preventions. Triggers on "postmortem
  this", "what went wrong", "root cause", "incident review". For a CLEAN completion a 2-line
  retro-note is enough — reserve the full postmortem for real incidents.
---

# Postmortem

For incidents, not routine wins. The aim is to fix the *system* that allowed the failure,
not to blame a person. If nothing actually went wrong, don't force one — write a 2-line
retro-note instead.

## Steps
1. **Timeline.** What happened, in order, with timestamps where you have them: when it
   started, when it was noticed, what made it worse, when it resolved. Use real evidence
   (logs, commits, messages) — don't reconstruct from memory alone.
2. **Symptom vs root cause.** Keep asking "why" past the first answer. The bug wasn't the
   root cause; the reason it shipped *undetected* usually is — a missing test, a gate that
   didn't exist, a wrong assumption about real data, a step that relied on willpower.
3. **What caught it / what should have.** Which check found it? Which check *should* have but
   didn't? That gap is the most valuable output.
4. **Lessons → preventions.** Map each lesson to a concrete prevention, **mechanical where
   possible** (a test, a gate, an automated check). Self-discipline alone decays — prefer
   "add a check that fails the build" over "be more careful next time".

## Output
- 3-5 sentence summary: what broke, root cause, blast radius.
- Timeline.
- Root cause (the system gap, not the symptom).
- Concrete preventions, each owned and ideally mechanical.

## Note
A heavy postmortem on every clean ship produces shallow theatre and gets abandoned. Scale
depth to what actually happened.
