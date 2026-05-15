# Walton Schemes — Classifier and Critical Questions

This reference is loaded by the `argument-audit` skill during scheme detection
(Phase 4 of the audit flow). It covers exactly four Walton argumentation schemes
plus a fallback note. The skill body should treat this file as the single source
of truth for which schemes the audit recognises and which Critical Questions
(CQs) it fires when a match is detected.

## Quick classifier table

Use this table as a fast first pass. Scan the input for the cue phrases in the
middle column; the right-hand column says which scheme to load.

| Cue family                                              | Surface phrases (non-exhaustive)                                                                                  | Scheme to load                   |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| Appeal to authority / testimony                         | "according to", "X says", "studies show", "research indicates", "as Dr. Y argues", "the consensus is"             | Argument from Expert Opinion     |
| Indicator / correlation language                        | "is a sign of", "indicates", "correlates with", "is associated with", "X tracks Y", "wherever X, also Y"          | Argument from Sign / Correlation |
| Comparison between two cases                            | "just like", "analogous to", "in the same way that", "this is similar to the case of", "by parity of reasoning"   | Argument from Analogy            |
| Goodness/badness of outcomes used as reason for/against | "if we do X, then Y will happen", "the upshot is", "this would lead to", "we should reject this because it would" | Argument from Consequences       |

Multiple schemes can be in play in a single passage. When that happens, fire
all matching scheme CQs, not just the first one detected.

---

## 1. Argument from Expert Opinion

Claim is supported by an authority's testimony. The authority is offered as the
warrant: we are asked to believe A because expert E (in field F) asserts A.

### Pattern (Argdown PCS)

```argdown
<ExpertOpinion>

(1) E is an expert in field F.
(2) E asserts that A is true (or false).
(3) A is within F.
-----
(4) A is plausibly true (or false).
```

### Detection cues

- "according to [name/title]"
- "[name] says/argues/contends/claims"
- "studies show", "research indicates", "the data show"
- "experts agree", "the consensus among Xs is"
- "as a [credential] I can tell you"
- A proper noun followed by an attributive verb ("Smith found", "Jones argues")
- A citation marker (footnote, parenthetical year) attached to a substantive
  claim that is not itself derived in the text

### Critical questions

The canonical Walton CQs for Expert Opinion are these seven. Fire any whose
answer is non-obvious from the source text.

1. **Expertise CQ** — Is E genuinely an expert in field F? (credentials,
   training, recognised standing)
2. **Field CQ** — Is the claim A actually within field F, or has E strayed
   outside their domain?
3. **Opinion CQ** — Did E in fact assert A? (quotation accuracy, paraphrase
   integrity, no straw-man of the expert)
4. **Trustworthiness CQ** — Is E personally reliable as a source? (history of
   honesty, no record of falsified work)
5. **Consistency CQ** — Is A consistent with what other experts in F say? Is
   there meaningful disagreement among qualified experts that the text glosses
   over?
6. **Backup-evidence CQ** — Is A supported by independent evidence beyond E's
   say-so? An expert opinion is weakest when it is the only support offered.
7. **Bias CQ** — Does E have a stake in the conclusion? (funding, ideological
   commitment, professional rivalry, prior public position)

### Notes

- "Studies show" without a specific study citation is a degenerate Expert
  Opinion appeal. Fire CQs 3 and 6 hard.
- A single-expert appeal in a field with known disagreement should always
  trigger CQ 5.
- Bias (CQ 7) is an **undercutting** consideration in Pollock's sense — it
  attacks the warrant without requiring you to assert ¬A.

---

## 2. Argument from Sign / Correlation

An observed feature is taken as a sign of an underlying state, OR an observed
correlation between two variables is taken as evidence of a causal relation.
Walton treats sign and correlation-to-cause as closely related schemes; we
fold them together here because the CQs overlap heavily.

### Pattern (Argdown PCS)

Sign form:

```argdown
<Sign>

(1) Feature F is observed in case C.
(2) F is generally a sign of underlying state S.
-----
(3) S is present in case C.
```

Correlation-to-cause form:

```argdown
<CorrelationToCause>

(1) X and Y are correlated in the observed data.
(2) The correlation is best explained by X causing Y.
-----
(3) X causes Y.
```

### Detection cues

- "is a sign of", "is a marker for", "indicates", "is symptomatic of"
- "where you see X, you see Y"
- "X is associated with Y", "X correlates with Y", "X is linked to Y"
- "X tracks Y", "X predicts Y" (when used non-statistically)
- Any move from "we observed X alongside Y" to "X causes Y"
- Clinical/diagnostic language transposed onto non-clinical claims

### Critical questions

1. **Strength CQ** — How reliably does F (or the X-Y correlation) actually
   indicate S (or causation)? Is the base rate of false positives addressed?
2. **Alternative-explanation CQ** — Could F be present without S? Could the
   X-Y correlation be explained by a confound, a third variable Z, reverse
   causation (Y → X), or coincidence given enough comparisons?
3. **Selection CQ** — How was the case (or the sample) chosen? Could the
   apparent sign/correlation be an artefact of which cases got measured?
4. **Magnitude CQ** — Is the correlation large enough to support the
   inferential weight being placed on it? A statistically significant but
   tiny effect rarely supports a strong causal claim.
5. **Mechanism CQ** — Is there an independent reason (mechanism, theory,
   prior evidence) to believe the proposed causal direction, or is the
   correlation the only support?
6. **Replication CQ** — Has the sign/correlation been observed in independent
   datasets, or is this a single observation being generalised?

### Notes

- The Mechanism CQ is the most powerful in practice. A correlation with no
  proposed mechanism collapses to "we noticed two things together" and
  rarely survives an undercutter.
- "Multiple comparisons" (selection from many possible correlations) is a
  special case of Selection CQ — fire it explicitly when the source mentions
  exploring large datasets.

---

## 3. Argument from Analogy

A claim about a target case is supported by a comparison to a similar source
case. The implicit warrant is that the two cases are similar in the relevant
respects.

### Pattern (Argdown PCS)

```argdown
<Analogy>

(1) Source case C1 has property P.
(2) Target case C2 is similar to C1 in relevant respects R.
-----
(3) C2 also has property P.
```

### Detection cues

- "just like", "in the same way that", "analogous to", "by parity of reasoning"
- "this is similar to the case of"
- "we already accept this for X, so we should accept it for Y"
- Historical precedent invocations ("remember what happened with...")
- Worked-example reasoning ("consider a parallel case...")
- Hypothetical comparisons ("imagine if instead we were talking about...")

### Critical questions

1. **Similarity CQ** — Are C1 and C2 actually similar in the respects R that
   matter for the property P being transferred?
2. **Relevant-difference CQ** — Are there disanalogies between C1 and C2
   that the analogy glosses over, and would those disanalogies block the
   transfer of P?
3. **Source-case truth CQ** — Is it actually true that C1 has property P?
   (Many bad analogies start from a misremembered or contested source case.)
4. **Counter-analogy CQ** — Is there an equally plausible analogy that
   points the opposite way? (e.g. "yes, but X is also like Z, which has ¬P")
5. **Scope CQ** — Even if the analogy supports P in some weak form, does it
   support the strength of the conclusion as actually stated?

### Notes

- Analogies often smuggle the conclusion into the choice of source case.
  Fire CQ 4 (counter-analogy) by default whenever the source case is rhetorically
  loaded ("just like the Nazis", "this is the new Vietnam").
- A failed Relevant-difference CQ is typically a **rebutting** defeater, not
  merely an undercutter — it can establish that C2 has ¬P.

---

## 4. Argument from Consequences

A claim is supported (or attacked) by appeal to the goodness or badness of the
consequences of accepting it. Note this is **not** automatically a fallacy —
consequentialist reasoning is legitimate when the question genuinely turns on
practical outcomes; it becomes a fallacy when used to settle a question of fact
("X can't be true because that would be bad").

### Pattern (Argdown PCS)

Pro form:

```argdown
<ArgumentFromConsequences>

(1) If we accept (or do) A, then good consequence G will follow.
(2) G is genuinely good (or, the good of G outweighs its costs).
-----
(3) We should accept (or do) A.
```

Con form:

```argdown
<ArgumentFromBadConsequences>

(1) If we accept (or do) A, then bad consequence B will follow.
(2) B is genuinely bad (or its badness outweighs any benefits of A).
-----
(3) We should not accept (or do) A.
```

### Detection cues

- "if we do X, then Y will happen"
- "the upshot is", "this would lead to", "the result would be"
- "we can't accept this because then..."
- "imagine the consequences if..."
- Slippery-slope phrasing ("first this, then that, then...")
- Floor/ceiling rhetoric ("this opens the door to", "this is the camel's nose")

### Critical questions

1. **Consequence-likelihood CQ** — How likely is consequence G (or B) actually
   to follow from accepting A? Is the causal chain spelled out, or assumed?
2. **Value CQ** — Is G genuinely good (or B genuinely bad), and good/bad to
   the degree the argument requires? Whose values are being assumed?
3. **Side-effects CQ** — Are there other consequences of A — possibly worse —
   that the argument leaves out? A one-sided ledger of outcomes is a red flag.
4. **Alternative-action CQ** — Even granting that G follows from A, is there
   a different action A\* that achieves G with fewer costs?
5. **Fact-vs-value CQ** — Is the conclusion a claim about what is true, or a
   claim about what we should do? Argument from Consequences is a fallacy
   when used to settle the former.
6. **Slippery-slope CQ** — If the consequence chain is multi-step, is each
   step independently supported, or is the argument relying on accumulated
   assumed transitions?

### Notes

- The Fact-vs-value CQ (5) is the single most important diagnostic. If the
  conclusion is "X is true" rather than "we should do X", consequentialist
  framing is a category error.
- Slippery-slope reasoning is a special form of Argument from Consequences
  with chained likelihoods. The chain compounds: P(B | A) might be 0.7, but
  P(D | A) via B → C → D might be 0.3.

---

## Schemes outside this set

The audit deliberately recognises only these four schemes. Walton's full
catalogue contains 60+ schemes (Argument from Position to Know, Argument from
Popular Opinion, Argument from Verbal Classification, Practical Reasoning,
Argument from Commitment, etc.). Restricting to four is a pragmatic choice:
these four cover the overwhelming majority of everyday argumentative prose
and the CQs are widely agreed upon.

When the agent encounters prose that doesn't match one of these four schemes,
it must NOT force a misfit (e.g. shoehorning a Practical Reasoning argument
into the Consequences scheme will fire the wrong CQs). Instead it falls back
to:

1. **Generic structural audit** — does each premise actually entail the
   conclusion? Are there missing premises? Are any premises themselves
   unsupported?
2. **Fallacy-list pass** — sweep for textbook fallacies (ad hominem,
   tu quoque, equivocation, composition/division, no-true-Scotsman, etc.)
   that aren't tied to a specific Walton scheme.
3. **Note the gap** — record in the audit's `Structural Issues` section that
   the argument did not match a recognised scheme, so the reader knows the
   CQ pass was skipped rather than passed.

The "no scheme matched" outcome is itself information: it usually means the
prose is either purely descriptive (no argument is being made) or that the
argument is unusual enough to warrant closer manual reading.
