# Methodology References

This file is loaded by the `argument-audit` skill at three distinct phases:

- **Phase 1 (Steelman):** Rapoport's Rules
- **Phase 6 (Rebut):** Pollock's defeasible-reasoning distinctions
- **Phase 8 (Digest sparring) and `aron-spar` follow-up:** Lincoln-Douglas debate primitives

Each section says when to apply the methodology, what to do, and what good
output looks like, so the skill body can refer to "the steelman shape from
methodology-references.md" rather than re-stating it inline.

---

## 1. Rapoport's Rules (Steelmanning Protocol)

### When to apply

**Always, before any rebuttal.** Rapoport's Rules run in Phase 1 of every
audit. The skill prepends a `## Steelman` section to every report; the
steelman is generated under these rules.

The rule is non-optional: even if the input prose is something the agent
finds weak or distasteful, the audit must steelman it before critiquing.
Skipping the steelman is a methodology violation in the same class as
skipping spec review.

### What to do — the four steps

Rapoport (via Dennett, _Intuition Pumps_, Ch. 33) gives four numbered
steps. Run them in order; each step has its own pass over the source
material.

1. **Re-express the position so well the proponent says "thanks".** Restate
   the argument in the strongest, clearest, most charitable form you can
   produce. The test: would the original author read your restatement and
   say "yes, that's exactly what I meant — and you put it better than I
   did"? If you cannot pass that test, you are not yet steelmanning.

2. **List points of agreement.** Enumerate the things the proponent says
   that you (or any reasonable reader) actually agree with. This is not
   throat-clearing politeness — it locates the genuine common ground and
   narrows the dispute to its real boundaries.

3. **State what you learned.** Explicitly note anything in the position
   that taught you something, surfaced a consideration you hadn't weighed,
   or refined your understanding. If you learned literally nothing from
   the source, double-check: that result is suspicious and usually means
   you are not reading charitably.

4. **Only then critique.** Rebuttal, undercutting, fallacy-flagging,
   structural complaints — all of these come _after_ steps 1-3, never
   before. The Phase 6 rebut step is gated on a passing Phase 1 steelman.

### Verification: the ideological Turing test

A steelman passes when it survives the **ideological Turing test**: if you
handed your steelman (with the rebuttal stripped off) to someone who
genuinely holds the original position, they should not be able to tell
that the steelman was written by an opponent rather than an ally.

Operationally, the agent self-checks by asking:

- Did I include the strongest version of each premise, including ones the
  source merely gestured at?
- Did I drop any rhetorical weakness in the source that wasn't load-bearing?
- Did I name the source's actual concern, not a strawman of it?
- Would a sympathetic reader of the source consider my restatement a fair
  paraphrase, or would they call it a hatchet job?

If any answer is "no" or "unsure", revise the steelman before proceeding.

### Output shape

Every audit report begins with a `## Steelman` section structured as:

```markdown
## Steelman

**The position, in its strongest form:**
<one-paragraph restatement that would pass the Turing test>

**Points of agreement:**

- <bullet>
- <bullet>

**What this position surfaces (what the audit takes from it):**
<one or two sentences naming what's genuinely useful in the source>
```

The steelman is rendered before any "Issues" or "Rebuttals" section. The
order is load-bearing: putting the critique first poisons the steelman.

---

## 2. Pollock's Defeasible-Reasoning Distinctions

### When to apply

**Phase 6 (Rebut) of the audit.** After scheme matching has fired its
Critical Questions and the structural pass has identified attack
candidates, every actual attack the audit makes must be classified as
either **rebutting** or **undercutting**, in John Pollock's sense
(_Defeasible Reasoning_, 1987 and later).

This classification is not bookkeeping — it determines what the rebuttal
needs to establish, and how it shows up in the Argdown output.

### The two kinds of defeater

#### Rebutting defeater

Attacks the conclusion **directly**. A rebutting defeater asserts ¬conclusion
or asserts a conclusion that conflicts with the original conclusion.

Example. Original: "The package was delivered (because the tracking page
says 'Delivered')." Rebutting defeater: "The package was not delivered
(I am standing on the porch and there is no package)."

A rebutting defeater commits you to the truth of an opposing claim. It is
the strong move: it requires evidence for ¬conclusion, but if it succeeds
it positively establishes the negation.

#### Undercutting defeater

Attacks the **inferential link** between premises and conclusion **without
contradicting any premise and without asserting ¬conclusion.**

Example. Original: "The wall is red (because it looks red to me)." Undercutting
defeater: "There is red light shining on the wall." This does not say the
wall is not red, and it does not say the wall does not look red — it says
the link from "looks red" to "is red" is broken in this particular case.

For Walton schemes, an undercutter typically targets the warrant. "E is
biased on this matter" is a paradigm undercutter against Argument from
Expert Opinion: it does not say A is false, and it does not say E did not
assert A; it says the link from "E asserted A" to "A is plausibly true"
is broken because E has a stake in the answer.

### How they appear in Argdown

By convention this skill uses the following Argdown forms:

**Rebutting** (attacks the claim node):

Two forms, depending on whether the attack is a bare counter-statement
or a named counter-argument.

Statement-level rebut (a counter-statement contradicts the claim):

```argdown
[OriginalClaim]: A is true.
  - [Counter]: A is false.
```

The `-` arrow is a contradicts-the-claim attack. Statement nodes use
square brackets only; do not wrap the counter in `<...>`.

Argument-level rebut (a named argument attacks the claim):

```argdown
[OriginalClaim]: A is true.
  <- <CounterArgument>

<CounterArgument>: reasons that A is false.
    (1) <premise>
    (2) <premise>
    -----
    (3) A is false.
```

The `<-` arrow reads "OriginalClaim is attacked by CounterArgument".
Use this form when the rebuttal has its own internal structure worth
naming and reasoning about; use the statement-level form when the
counter is a single asserted negation with no further support.

**Undercutting** (attacks an inference, not a claim):

Argdown gives two equivalent forms for an undercut relation: the
_incoming_ form (anchored under the target, reads "target is undercut
by ..."), and the _outgoing_ form (anchored under the attacker, reads
"attacker undercuts ..."). Pick whichever reads more naturally for the
surrounding prose; both produce the same parsed relation.

Incoming form — anchor the relation under the target argument:

```argdown
<ExpertOpinionInstance>

(1) E is an expert in F.
(2) E asserts A.
-----
(3) A is plausibly true.
    <_ <BiasUndercutter>

<BiasUndercutter>: E has a financial stake in A being accepted.
```

Read: "ExpertOpinionInstance is undercut by BiasUndercutter."

Outgoing form — anchor the relation under the attacking argument:

```argdown
<BiasUndercutter>: E has a financial stake in A being accepted.
    _> <ExpertOpinionInstance>
```

Read: "BiasUndercutter undercuts ExpertOpinionInstance."

In both forms the `<_` / `_>` arrows target the inferential step (the
named argument as a whole), not the conclusion or any individual
premise. The undercutter attacks the warrant — the link from premises
to conclusion — rather than the content of either end.

### When to prefer which

Use the following heuristic:

| Situation                                                               | Prefer       | Why                                                                                                      |
| ----------------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------- |
| You have positive evidence the conclusion is false                      | Rebutting    | Strongest move; establishes ¬conclusion.                                                                 |
| Premises look defensible but the inference smells wrong                 | Undercutting | Don't pick a fight with premises you can't beat; cut the warrant instead.                                |
| You suspect bias, motivated reasoning, or confound                      | Undercutting | These attack the link, not the content.                                                                  |
| A Walton CQ fired at the warrant level (Bias CQ, Strength CQ, Field CQ) | Undercutting | The CQ itself targets the warrant.                                                                       |
| A Walton CQ fired at the content (Source-case truth CQ, Field-mismatch) | Rebutting    | The CQ disputes a premise / the conclusion's domain.                                                     |
| You can do both                                                         | Both         | Stack them. Pollock allows multiple defeaters; the skill should fire each separately, not collapse them. |

The default when uncertain is undercutting: it makes a smaller commitment
and is harder for the proponent to flip back on you.

### Output shape

In the audit report's `## Rebuttals` section, each rebuttal appears as a
small Argdown block prefixed with its Pollock classification:

````markdown
### Rebuttal 1 — Undercutting (Bias CQ)

`<ExpertOpinionInstance>` is undercut: the cited expert is the principal
investigator on the funded study being defended.

```argdown
<ExpertOpinionInstance>
    <_ <BiasUndercutter>

<BiasUndercutter>: E is the PI on the study under discussion.
```
````

Every rebuttal carries its classification (`Rebutting` or `Undercutting`)
in the heading. Mixed attacks (one of each against the same target) are
rendered as separate sub-rebuttals, not merged.

---

## 3. Lincoln-Douglas Debate Primitives

### When to apply

**Phase 8 (Digest sparring) of the audit, and the `aron-spar` follow-up
command** that lets a user continue an audit turn-by-turn against the
agent. LD primitives shape how the agent behaves in adversarial back-and-forth
mode, where the goal shifts from "produce a complete audit" to "win or lose
specific exchanges over multiple turns".

LD primitives are not used in single-pass audits without a sparring component.

### The four primitives

#### Dropped arguments are conceded

In LD, if your opponent makes an argument and you fail to respond to it,
the argument stands as conceded — it counts in the final ballot as if you
agreed with it. The skill enforces this by tracking which sub-claims have
been answered vs. ignored across sparring turns.

Operationally: at each turn, the agent maintains a **ledger of live
sub-claims** from previous turns. A sub-claim is "live" until it is either
answered (with mitigate / outweigh / concede-and-redirect) or dropped. The
ledger is rendered at the end of each agent turn so the human sparring
partner can see what has and hasn't been addressed.

If the agent itself drops a sub-claim, it must say so explicitly:
"conceded — moving on", not silently ignore.

#### Collapse to crux

Late in a sparring continuation (typically turn 3 onward), the agent
narrows the discussion to the one or two arguments that actually decide
the question. Most arguments have peripheral disputes that don't affect
the verdict; collapsing identifies and drops these.

Heuristic for what to collapse to: the sub-claim(s) whose resolution would
flip the overall verdict. If both sides could agree on every other point
but still disagree on this one, that's the crux.

The collapse is announced explicitly: "the dispute reduces to X. Other
points are not load-bearing for the verdict."

#### Voting issues

At the end of each agent sparring turn, the agent renders an explicit
"voting issues" callout: a short, numbered list of the reasons a neutral
reader should currently rule for the agent's side. This makes the
agent's case auditable turn by turn — if a voting issue from turn 2
disappears in turn 4 with no explanation, that's a signal the agent
quietly abandoned ground.

Format:

```markdown
**Voting issues this turn:**

1. <one-line reason>
2. <one-line reason>
3. <one-line reason>
```

Three is a soft target; one is acceptable late in a collapse, five is
usually too many (suggests the dispute hasn't been collapsed yet).

#### Mitigate / outweigh / concede-and-redirect

These are the three honest responses to a strong opposing point. Use
exactly one per opposing point — mixing them muddies the response.

- **Mitigate.** Accept the opposing point's existence but reduce its
  magnitude. "Yes, but the effect is smaller than claimed because [reason]."
  Used when the opposing point is true but overstated.

- **Outweigh.** Accept the opposing point in full and argue that another
  consideration overwhelms it. "Granted X, but Y is more important here
  because [reason]." Used when the opposing point is true and your case
  survives anyway because something else dominates.

- **Concede-and-redirect.** Accept the opposing point in full, drop the
  ground it covers, and shift to a different sub-claim that the opposing
  point doesn't reach. "Fair — I'll grant X. The remaining dispute is over
  Y, where [argument]." Used when the opposing point is decisive against
  the original framing but the audit's bottom line can still be defended
  on different grounds.

The dishonest fourth response — pretend the opposing point wasn't made,
or restate your original claim louder — is the single most common
sparring failure and the one the agent must avoid most carefully.

### Output shape

A sparring turn from the agent has this structure:

```markdown
### Turn N — agent

**Ledger update:**

- (from turn N-1) sub-claim X: answered → mitigated, see below
- (from turn N-1) sub-claim Y: live, not yet addressed by either side
- (new this turn) sub-claim Z: agent introduces

**Response to opposing turn:**

- On X: [mitigate | outweigh | concede-and-redirect] — <argument>
- On Y: [mitigate | outweigh | concede-and-redirect] — <argument>

**Crux (if collapsing):**
The dispute reduces to <claim>. Other points are not load-bearing.

**Voting issues this turn:**

1. <reason>
2. <reason>
3. <reason>
```

The ledger update at the top makes dropped-argument tracking visible.
The voting issues at the bottom give the human a clean comparison point
across turns.

### Notes on applying LD honestly

LD primitives can be weaponised — used to "win" a sparring round at the
cost of being right. The agent should not do this. In particular:

- Do not collapse to crux prematurely (turn 1 or 2) just to drop ground
  the agent finds inconvenient.
- Do not declare a sub-claim "dropped" by the human partner if the human
  was simply slow to respond; ask first.
- Voting issues must be accurate one-line summaries, not slogans.
- Concede-and-redirect must actually concede; do not concede in name only
  while continuing to argue the conceded point in subsequent turns.

The goal of sparring inside an audit is to stress-test the steelman, not
to score points. The same Rapoport-rules charity from Phase 1 still
applies during sparring.
