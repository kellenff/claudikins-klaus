# Sparring trace fixture (TEMPLATE)

> **THIS IS A TEMPLATE.** It is not a real captured transcript. Task T8
> (smoke-verify) is responsible for filling this file in by running
> `/claudikins-klaus:aron-spar` against a prior audit output and pasting
> the resulting three-turn transcript into the placeholders below.
>
> Do not treat the placeholder text as ground truth. The expected shape
> of each turn is documented inline so that T8 (and any future fixture
> regeneration) knows what good looks like.

## What gets captured here

This file holds a single three-turn sparring transcript produced by
`/claudikins-klaus:aron-spar` against an audit finding from one of the
prose fixtures in this directory (typically `hidden-assumption.md`,
`equivocation.md`, or `unsupported-leap.md`). The transcript is the
artefact SC-8b verifies: did the spar command produce the expected
Rapoport / Walton / press-or-collapse structure, and did it terminate
correctly?

The transcript should be pasted **verbatim** from the spar session,
including any inline markers the command emits.

---

## Turn 1 — Rapoport-style steelman

**Expected to appear in this turn:**

- A steelman of the audited argument that the original author would
  recognise and endorse — i.e. a charitable reconstruction, not a
  setup-for-knockdown paraphrase.
- An explicit acknowledgement of at least one point on which the
  audited argument is correct or insightful.
- The drop-press-collapse marker (`[drop|press|collapse]`) at the end of
  the turn, recording the spar's posture toward the steelman.
- No new attacks introduced yet; Turn 1 is reconstruction only.

**Transcript placeholder:**

```
<paste Turn 1 output from /claudikins-klaus:aron-spar here>
```

---

## Turn 2 — Fired Walton critical question

**Expected to appear in this turn:**

- A specific Walton critical question fired against the steelman from
  Turn 1, named explicitly (e.g. "CQ3 for argument-from-expert-opinion:
  is the cited source actually expert in the relevant field?").
- The CQ must be one that the steelman cannot dismiss out of hand —
  i.e. it targets a real load-bearing element of the argument.
- The drop-press-collapse marker recording how the spar handled the
  steelman's response (or anticipated response) to the CQ.
- If the audited argument is corpus-mode, the CQ should engage with the
  contradiction surfaced in `corpus-contradiction.expected.md`, not
  bypass it.

**Transcript placeholder:**

```
<paste Turn 2 output from /claudikins-klaus:aron-spar here>
```

---

## Turn 3 — Press / collapse decision

**Expected to appear in this turn:**

- A decision: press the CQ further, collapse the line of attack, or
  drop it and open a new line. The decision must be justified in one
  to two sentences.
- The drop-press-collapse marker on this turn matching the decision.
- If the user has requested a verdict at any prior point, a
  voting-issues callout naming which issues the verdict turns on
  (e.g. "Verdict turns on: validity of the holdout design, transfer of
  the historical analogy.").
- A check against the termination conditions below; if any has fired,
  the turn must record which one and stop.

**Transcript placeholder:**

```
<paste Turn 3 output from /claudikins-klaus:aron-spar here>
```

---

## Termination conditions checklist

The spar terminates on the first of these to fire. T8 should tick the
condition that ended the captured session and leave the others unticked.

- [ ] **Concede.** The audited argument's defender (real or simulated)
      conceded the load-bearing point.
- [ ] **Verdict request.** The user explicitly asked for a verdict; the
      spar produced a voting-issues callout and stopped.
- [ ] **5-turn stall.** The spar reached five turns without progress on
      the central disagreement. (Note: this template only structures the
      first three turns; turns four and five, if they occur, should be
      appended below before the checklist is ticked.)
- [ ] **/end-spar.** The user issued the `/end-spar` command.

---

## Notes for T8

- If the spar produces fewer than three turns because a termination
  condition fires early (concede or verdict-request on Turn 1 or Turn 2),
  leave the unused turn placeholders in place but mark them
  `<not reached: terminated on Turn N by <condition>>`.
- If the spar produces more than three turns (continued past Turn 3
  without termination), append additional `## Turn N` sections following
  the same shape: expectations block, transcript placeholder.
- The drop-press-collapse marker must appear on every turn, including
  any appended ones. A missing marker is a SC-8b failure.
