# Audit Report Template

This is the canonical Markdown skeleton the `argument-audit` skill renders
when invoked with the `--report` flag. The template below is rendered into
a file (typically `<slug>-audit.md`) by substituting the `{{...}}` tokens.

The eleven sections appear in the order shown. Order is load-bearing:
the Verdict callout is read first, the Steelman comes before any critique
(Rapoport ordering), and the Cross-document synthesis is rendered only in
corpus mode.

The Verdict callout MUST be the first line of the file, before any
heading. It is rendered as a Markdown blockquote so it visually pops
even in renderers that don't style verdicts specially.

---

## Section-by-section commentary

The placeholders below correspond to fields the skill computes during
the audit pass. Each is described above its first use in the template.

- `{{verdict}}` — one of `Sound`, `Conditional`, `Defeated`, `Indeterminate`.
  Sound = no surviving attacks, all CQs answered. Conditional = survives
  but with caveats the reader must accept. Defeated = at least one
  rebutting defeater stands. Indeterminate = audit could not reach a
  verdict (insufficient information, no scheme matched, etc.).
- `{{verdict_summary}}` — one sentence, no trailing period required.
- `{{slug}}` — kebab-case identifier the audit was invoked with.
- `{{audited_at}}` — ISO-8601 timestamp.
- `{{input_source}}` — path or URL of the audited material.
- `{{mode}}` — `prose`, `argdown`, or `corpus`.
- `{{steelman_paragraph}}` — single paragraph passing the ideological
  Turing test (see methodology-references.md, Rapoport's Rules).
- `{{points_of_agreement}}` — bullet list, one item per line.
- `{{what_we_learned}}` — one or two sentences.
- `{{prose_issues}}` — bullets; each item ends with `(L<start>-L<end>)`.
- `{{structural_issues}}` — bullets; each item names the Argdown
  statement title it concerns, e.g. `[ClaimID]`.
- `{{critical_questions}}` — bullets; each item carries the scheme name
  and a confidence label (`high` / `tentative`).
- `{{rebuttals}}` — list of small Argdown blocks, each tagged
  `Rebutting` or `Undercutting` per Pollock (see methodology-references.md).
- `{{traceability_table}}` — three-column table of every cited claim.
- `{{digest_sparring}}` — 3 strongest objections + collapsed crux + voting issues.
- `{{cross_doc_synthesis}}` — corpus-mode only; shared claims, attack
  relations across docs, grounded extension partition.
- `{{interactive_footer}}` — rendered only when the audit was invoked
  with `--interactive`.

---

## The template

```markdown
> **Verdict:** {{verdict}} — {{verdict_summary}}

# Audit: {{slug}}

- **Audited at:** {{audited_at}}
- **Input source:** {{input_source}}
- **Mode:** {{mode}}

## Steelman

**The position, in its strongest form:**
{{steelman_paragraph}}

**Points of agreement:**
{{points_of_agreement}}

**What this position surfaces:**
{{what_we_learned}}

## Prose Issues

{{prose_issues}}

## Structural Issues

{{structural_issues}}

## Critical Questions

{{critical_questions}}

## Rebuttals

{{rebuttals}}

## Source traceability

| Claim ID | Line range | Excerpt (≤80 chars) |
| -------- | ---------- | ------------------- |

{{traceability_table}}

## Digest Sparring

{{digest_sparring}}

## Cross-document synthesis

{{cross_doc_synthesis}}

{{interactive_footer}}
```

---

## Rendering rules

These rules govern how the skill substitutes into the template. They are
enforced by the skill body, not by the template itself; they are documented
here so the renderer and the human reading the output share an understanding
of what each section can and cannot contain.

### Verdict callout (FIRST LINE — MANDATORY)

The blockquote `> **Verdict:** ...` MUST be the first line of the rendered
file, preceding the `# Audit:` heading. Tooling that consumes audit
reports (e.g. the `aron-spar` follow-up) reads the verdict from this
exact position. Do not prepend front-matter, comments, or whitespace.

If the audit cannot determine a verdict, render `Indeterminate` rather
than omitting the callout. An audit report without a verdict callout is
malformed.

### Steelman section

Always rendered, never omitted. Even an audit with verdict `Defeated`
includes a sincere steelman; the steelman is what the verdict is being
applied to. See methodology-references.md §1 for the four-step shape and
the ideological-Turing-test acceptance criterion.

### Prose Issues / Structural Issues

These are bullet lists. If a section is empty (no issues found), render
an explicit single bullet `- _none_` rather than leaving the section
blank. An empty section is ambiguous between "we found nothing" and "we
forgot to look".

Each Prose Issue bullet ends with a parenthetical line range:
`- vague term "robust" not defined (L42-L43)`. Each Structural Issue
bullet names the Argdown statement title it concerns:
`- [MainClaim]: no support offered for premise (2)`.

### Critical Questions section

Each bullet identifies the scheme that fired the CQ and a confidence
label:

```markdown
- **(Expert Opinion, high)** Bias CQ: cited expert is funded by the
  organisation whose product is endorsed.
- **(Sign/Correlation, tentative)** Mechanism CQ: no causal mechanism
  proposed for the X→Y inference.
```

Confidence is `high` when the CQ has a clear answer in the source's
disfavour; `tentative` when the CQ raises a real concern but the audit
cannot fully resolve it from the available material.

### Rebuttals section

Each rebuttal is a third-level heading with its Pollock classification,
followed by a short Argdown block:

````markdown
### Rebuttal 1 — Undercutting (Bias CQ)

`<ExpertOpinionInstance>` is undercut: <one-sentence reason>.

```argdown
<ExpertOpinionInstance>
    <_ <BiasUndercutter>

<BiasUndercutter>: <claim>.
```
````

If no rebuttals apply, render `_no surviving rebuttals_` as the section body.

### Source traceability

Every claim referenced in any earlier section MUST appear in this table.
The table is the audit's anchor to the source text — if a claim shows up
in `## Critical Questions` or `## Rebuttals` without a row here, the
audit is not traceable and the renderer should refuse to emit it.

The excerpt column is truncated to 80 characters with a trailing `…` if
truncation occurred. Line ranges use the input file's line numbering.

### Digest Sparring

Three sub-elements, in this order:

```markdown
**Three strongest objections:**

1. <objection>
2. <objection>
3. <objection>

**Collapsed crux:**
<one or two sentences naming the sub-claim that decides the dispute>

**Voting issues:**

1. <reason>
2. <reason>
3. <reason>
```

This section is rendered in every audit, not just sparring sessions —
it serves as the agent's pre-emptive stress test of its own conclusion.

In single-pass audits, only the crux-collapse and voting-issues LD
primitives apply; the mitigate/outweigh/concede-and-redirect and
dropped-arguments-conceded primitives from `methodology-references.md`
§3 only fire during multi-turn `aron-spar` sessions.

### Cross-document synthesis (corpus mode only)

Rendered only when `mode` is `corpus`. In `prose` and `argdown` modes,
omit the section entirely (do not render an empty heading).

When rendered, it contains:

- **Shared claims:** claims that appear (in equivalent form) in multiple
  source documents, with a list of which documents.
- **Attack relations across documents:** a list of `<doc_a>:<claim_a>
attacks <doc_b>:<claim_b>` lines, classified Rebutting/Undercutting.
- **Grounded extension partition:** the IN/OUT/UNDEC partition computed
  by the Dung extension tool over the cross-document attack graph.

### Interactive footer

Rendered only when the audit was invoked with `--interactive`. Format:

```markdown
_To continue this argument turn-by-turn, run:_ `/claudikins-klaus:aron-spar {{slug}}`
```

The footer is the last line of the file. When `--interactive` is not set,
omit the footer entirely (no trailing blank section).

---

## Minimum viable rendering

A correctly rendered audit always contains, at minimum:

1. The Verdict callout (line 1)
2. The `# Audit: <slug>` heading with metadata
3. A `## Steelman` section with non-empty body
4. The `## Source traceability` table with at least one row

If any of these four is missing, the renderer should fail loudly rather
than emit a partial report. All other sections may render `_none_` or
`_no surviving rebuttals_` if genuinely empty, but the four above are
load-bearing.
