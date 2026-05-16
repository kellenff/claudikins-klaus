---
name: argument-audit
description: Use when auditing logical arguments — finding hidden assumptions, equivocations, unsupported leaps, and surviving rebuttals — in either prose or structured Argdown documents. Runs an 8-phase methodology with bidirectional prose↔Argdown via the @casualtheorics/argdown-plugin, Walton scheme classification, Pollock rebut/undercut distinctions, and Lincoln-Douglas digest sparring. Triggers on requests to audit/critique/find-holes-in/pressure-test an argument.
version: 1.0.1
---

# Argument Audit Methodology

You are an argument auditor. Follow this 8-phase methodology with extreme rigour. Do not skip phases. Structural data flows only from `mcp__argdown-mcp__*` MCP responses — never parse prose output from sub-skills as structured data.

This skill is persona-neutral. Agents that wrap it (e.g. Aron) may layer narration on top, but the verdict shape, phase order, and artifact contracts are owned here.

The reference docs cited throughout live alongside this skill:

- `references/argdown-ast-fixture.md` — the actual `export_json` AST shape (real round-trip). Documents that `export_json` silently drops YAML metadata; Phase 4 accounts for this.
- `references/walton-schemes.md` — the four-scheme classifier table plus critical-questions catalogue (expert-opinion, sign/correlation, analogy, consequences). Used by Phases 4 and 5.
- `references/methodology-references.md` — Rapoport's rules (Phase 1), the Pollock rebut/undercut preference table (Phase 6), and the LD digest-sparring primitives (Phase 8).
- `references/audit-report-template.md` — the 11-section Markdown skeleton rendered when `--report` is set.

Load them on demand; you do not need to read them up-front.

## Phase 1: Steelman (Rapoport's Rules)

Before any critique, present the opponent's argument at its strongest. This is non-negotiable: a critique that lands on a weak version of the argument is a critique of nothing.

Apply the four steps from `references/methodology-references.md` §1:

1. Re-express the position so well the proponent would say "thanks, I wish I'd put it that way".
2. List the points of agreement (especially non-trivial or widely-shared ones).
3. State what you have learned from the argument.
4. Only after the above three steps, proceed to critique — and even then, the critique itself is deferred to Phases 3 through 6.

Operational notes:

- The steelman is for the proponent to recognise; if you can't articulate it without slipping into critique, you do not yet understand the argument well enough to audit it.
- If the user input is empty (interactive mode with no argument supplied), prompt the user for the argument first. Do not steelman air.
- If the input is multiple documents (corpus mode), steelman each document independently before merging in Phase 7.

Practical shape of a steelman:

- One paragraph in the proponent's voice, no hedging words ("seems", "perhaps", "in some sense"). The proponent uses their full claim.
- A short list of the concessions the auditor is willing to grant up-front. These concessions become assumptions in Phase 6 — anything granted here cannot be attacked later.
- One sentence on what the argument's strongest framing teaches the auditor about the domain.

**Output:** A `Steelman` section in the inline narration / report, stored for re-use in the Phase 8 digest.

## Phase 2: Validate-or-Extract

**Argdown MCP probe procedure.** Attempt to call `mcp__argdown-mcp__parse` (or the tool-executor-namespaced equivalent `mcp__plugin__casualtheorics_argdown-plugin_argdown-mcp__parse`) with a trivial input: `{kind: "inline", source: "[Test]: hello"}`. Three outcomes:

1. **Successful response with parse counts** → argdown plugin available; proceed to the input-shape branch below.
2. **Tool call fails with "tool not found" / "tool not in inventory"** → argdown plugin not bound to your tool surface. Emit the install message below and continue in degraded mode.
3. **Tool call returns an error from the plugin itself** (e.g., parse error on `[Test]: hello`) → unexpected; surface the error verbatim to the user and degrade to prose-only mode rather than retry blindly.

If degraded mode triggers, emit verbatim:

> Argdown plugin not installed; run `/plugin install @casualtheorics/argdown-plugin` and retry. Falling back to prose-only critique without structural verification.

…and continue from Phase 3 in degraded mode. Degraded mode does NOT mean "Indeterminate verdict by default" — a competent prose-only structural pass can still produce `Conditional` or `Defeated` when concrete findings warrant. `Indeterminate` is the fallback for cases where prose alone cannot resolve the structure, not the automatic outcome of degraded mode. The Verdict callout remains mandatory either way.

If available, branch on input shape:

- **Argdown input** (file ends in `.argdown` or input is recognisably Argdown syntax): call `mcp__argdown-mcp__parse`. If diagnostics report errors, surface them to the user verbatim and continue with whatever parsed cleanly. Treat a partial parse as a structural finding in its own right — record it for Phase 8.
- **Prose input**: invoke the `extract-argument` argdown sub-skill (or call the underlying argdown extraction tooling) to convert prose → Argdown. Preserve the original prose alongside the extracted Argdown so the source-traceability table in the report can show both sides. The extraction is necessarily lossy; do not treat the Argdown as authoritative over the original prose for quotation purposes.

You MAY call `mcp__argdown-mcp__parse` again on the extracted Argdown to confirm it round-trips cleanly. If extraction produces unparseable Argdown, that is a Phase 8 finding.

Detection heuristics for input shape:

- Begins with `===`, `+`, `-`, `<`, `[`, or a `# Title` line followed by Argdown structure markers → Argdown.
- Ends in `.argdown`, `.ad`, or `.argd` file extension → Argdown.
- Otherwise → prose, extract first.

When in doubt, attempt `parse` first and fall back to extraction if the diagnostics are dominated by syntax errors at column 0 of every line (a strong signal the input is prose rather than malformed Argdown).

**Output:** `validated_argdown` (string), `parse_diagnostics` (list of any warnings/errors), and (for prose input) `original_prose` retained for source-traceability.

## Phase 3: Structural audit

Walk the parsed structure using the AST shape documented in `references/argdown-ast-fixture.md`. Concretely:

- Call `mcp__argdown-mcp__export_json` to obtain the full AST.
- Traverse the `statements`, `arguments`, and `relations` collections per the fixture's documented shape.

Hunt for these structural defects:

- **Unsupported premises:** premises that have no incoming `support` relation. These are pure assertions hiding inside a proof.
- **Dangling assumptions:** statements asserted somewhere but never used as premise, conclusion, or attack target. Often a sign of incomplete extraction or a hidden assumption the proponent forgot to wire in.
- **Surviving attackers:** relations targeting the top-level claim or main argument that have no counter-attack — these are live threats by default until Phase 8 says otherwise.
- **Orphan arguments:** arguments declared but never referenced from the top-level claim's support chain.
- **Cycles:** support cycles among premises (rare but real; mark for human review).

You MAY delegate to the `find-unsupported-premises` and `trace-argument` argdown sub-skills via Skill() calls, BUT do not parse their prose output as structured data (R-9). All structured findings must come from the MCP responses you call directly. Sub-skill output is supplementary commentary only.

Severity assignment heuristic:

- `high` — unsupported premise inside the main argument's support chain, or a surviving attacker on the top-level claim.
- `medium` — dangling assumptions, or surviving attackers on sub-arguments that don't directly support the conclusion.
- `low` — orphan arguments, structural noise, or support cycles among premises that don't affect the conclusion's derivation.

Severity feeds the Phase 8 ranking; it is not a verdict on its own.

**Output:** a structured findings list. Each finding has the shape `{type, target_statement_title, severity}` where `type` ∈ {`unsupported_premise`, `dangling_assumption`, `surviving_attacker`, `orphan_argument`, `support_cycle`} and `severity` ∈ {`low`, `medium`, `high`}.

## Phase 4: Classify schemes

For each top-level argument or premise-conclusion-structure (PCS) in the parsed document, attempt Walton scheme classification.

**Critical constraint — the YAML-drop problem:**

`mcp__argdown-mcp__export_json` silently drops YAML metadata from `data:` blocks (see the warning at the top of `references/argdown-ast-fixture.md`). If you need scheme-tag hints from `data:` blocks, **re-parse the YAML out of the raw Argdown source text — do not look for it in the AST.** Read the source file (or `validated_argdown` string) again and scan for the YAML fences yourself.

Procedure:

- For each argument, examine the source prose plus any re-parsed YAML metadata for cues from `references/walton-schemes.md`. The four catalogued schemes are: expert-opinion, sign/correlation, analogy, and consequences.
- Assign up to two best-guess schemes per argument, each with a confidence label: `high` or `tentative`. Two schemes is the cap — an argument matching three or more is almost certainly misclassified or under-decomposed.
- Arguments that don't match any of the four catalogued schemes degrade to "generic structural critique only" — flag them as `scheme: unclassified` so Phase 5 knows to skip CQ-firing for them.

The four-scheme set is intentionally minimal. The wider Walton taxonomy has dozens of schemes; v1 sticks to the four highest-yield ones for audit work. Unclassified arguments are not "failures" — they just don't get the CQ-firing treatment in Phase 5.

Quick-cue table (full version in `references/walton-schemes.md`):

- "According to X, who is an expert in domain D..." → expert-opinion.
- "X happened, therefore Y must have caused it" or "X correlates with Y, so X explains Y" → sign/correlation.
- "X is like Y, so what holds for Y holds for X" → analogy.
- "If we do X, then good/bad consequence Y will follow" → consequences.

Cue matching is heuristic; confirm by checking whether the scheme's critical questions actually apply to the argument before assigning even a `tentative` confidence.

**Output:** scheme classification per argument as a list of `{argument_title, schemes: [{name, confidence}]}` records.

## Phase 5: Fire critical questions

For each classified argument from Phase 4:

- Look up the matched scheme(s) in `references/walton-schemes.md` and fire each catalogued critical question (CQ) against the argument.
- Phrase each CQ as a direct question to the proponent, citing the argument it targets.
- If an argument has two tentative schemes, fire CQs for both as parallel lines of attack (each CQ records which scheme it came from). Don't pre-emptively pick one scheme over the other; let the CQs themselves expose which scheme actually fits.
- For `scheme: unclassified` arguments, skip CQ-firing entirely — the Phase 4 gate is intentional.

Each fired CQ becomes a candidate hole-finding entry that Phase 6 may convert into a rebuttal. A CQ that the original argument already answers explicitly should be marked `status: answered` rather than `status: open`, with a pointer to the answering premise.

CQ-firing discipline:

- Fire every catalogued CQ for the matched scheme. Do not pre-filter "obviously answered" CQs without checking the source — what feels obvious to the auditor may be exactly what the proponent assumed without arguing for.
- An `answered` CQ still appears in the output, with the answering premise cited. The audit's value is in the trail of what was checked, not just what was found wanting.
- A CQ that cannot be evaluated (because the relevant evidence is not in scope of the document) is marked `status: open` with a note — silent omission is a defect.

Example: for an expert-opinion argument citing Dr Y on climate sensitivity, the CQ "Is Y a genuine expert in the relevant field?" might be `answered` (the doc establishes Y's credentials) while "Is Y's claim consistent with other experts?" might be `open` (the doc doesn't address consensus).

**Output:** list of fired CQs, each shaped `{argument_title, scheme, cq_name, cq_text, status: open | answered, answering_premise?: string}`.

## Phase 6: Rebut (Pollock)

For each surviving hole from Phases 3 and 5, generate a Pollock-typed counter.

The rebut/undercut distinction matters because they attack different things:

- A **rebut** attacks the conclusion directly ("not-C").
- An **undercut** attacks the inference itself ("the premises don't actually support C in this case"), leaving the conclusion's truth-status undetermined rather than falsified.

Procedure:

- Decide rebut-vs-undercut using the preference table in `references/methodology-references.md` §2. The general rule: if you can attack the conclusion's truth, rebut; if you can only attack the warrant from premises to conclusion, undercut.
- **Rebut:** generate a `[Counter]: <claim>` Argdown statement attacking the conclusion.
- **Undercut:** generate a `<Undercutter>` argument that attacks the inference itself, using the corrected `<_` arrow directionality from `references/methodology-references.md` (anchor the `<_` arrow under the target argument, not above it). This is the most common Argdown mistake; double-check it.
- You MAY invoke the `rebut-argument` argdown sub-skill for snippet boilerplate, but treat its output as a template only — the structural classification (rebut vs undercut) is yours to assign.

Each generated counter must specify what it attacks, so the verdict logic in Phase 8 can decide whether it defeats the original conclusion or merely qualifies it.

Worked example of the directionality trap (the most common mistake):

- Wrong: placing `_>` from the undercutter to the target argument (this is a support arrow).
- Wrong: placing `<_` above the undercutter pointing to the target (parses but inverts the relation).
- Right: anchor the `<_` arrow under the target argument, with the undercutter's title on the right-hand side. See `references/methodology-references.md` for the canonical snippet.

Self-check: after generating each undercut, mentally re-read the snippet as "the target argument is undercut by the undercutter" — if the snippet doesn't read that way, the directionality is wrong.

**Output:** a list of rebuttal Argdown snippets, each Pollock-classified as `rebut` or `undercut` with the target it attacks.

## Phase 7: Cross-document synthesise (corpus mode only)

Skip this phase entirely if the input is a single document. Corpus mode triggers when the user supplies multiple file paths or a glob.

For corpus inputs:

- Audit each document independently via Phases 1-6 first; do not let cross-document reasoning leak into the per-document audits.
- Build a combined Argdown buffer that includes both (or all) documents as sub-sections, with stable section prefixes per document so cross-references resolve cleanly.
- **Shared-claim detection:** literal-text equivalence only — byte-exact substring match across documents. Semantic equivalence (paraphrase detection, coreference resolution, embedding similarity) is **out of scope for v1.** This is a deliberate scope limit; do not silently expand it.
- For shared claims where one document asserts the claim and another attacks it, mark the pair as a cross-document conflict.
- Run `mcp__argdown-mcp__dung_extensions` over the union buffer to compute the grounded extension (IN/OUT/UNDEC partition under Dung's abstract argumentation framework).
- Report which top-level claims land in IN (survive all attacks), OUT (defeated), or UNDEC (mutually-defeating cycles).

The grounded-extension output is authoritative for cross-document conflicts. If it says a claim is OUT, Phase 8's verdict for that claim is `Defeated` regardless of what the per-document audit concluded.

Section-prefix convention for the combined buffer:

- Use document filenames (sans extension) as the section prefix for each document's contents.
- Within each document section, retain the original argument titles unchanged.
- Cross-document references use the form `<DocPrefix>::<ArgumentTitle>` so the union buffer parses without title collisions.

If two documents use literally identical argument titles for distinct arguments, that is itself a cross-document finding worth surfacing — it usually indicates terminology drift that the corpus author hasn't resolved.

**Output:** a cross-document synthesis section containing the list of shared claims, the cross-document attack relations, and the grounded-extension partition.

## Phase 8: Narrate verdict + digest sparring

Decide the verdict. Exactly one of:

- **Sound** — no surviving rebuttals, all CQs answered, no unsupported premises.
- **Conditional** — the argument holds if specific assumptions are granted; name those assumptions explicitly in the one-sentence summary.
- **Defeated** — at least one rebut or undercut survives that defeats the conclusion. (For corpus mode: any claim landing in OUT under the grounded extension forces this verdict.)
- **Indeterminate** — input is too thin or too ambiguous to judge, or the Argdown plugin was unavailable and no structural verification could be performed.

**The Verdict callout MUST be the first line of the rendered audit body**, before any persona narration, framing, or preamble inside the audit. Tool invocations during Phases 0–7 (probing the argdown plugin, calling `export_json`, invoking sub-skills, reading source files) are NOT part of the rendered audit body and may precede the callout. Persona pre-ambles described in the wrapping agent (e.g. board state, stage directions, opening flourish) MUST appear AFTER the Verdict callout, never before. Render it as a Markdown blockquote:

```markdown
> **Verdict:** Defeated — <one-sentence summary>
```

Then narrate the audit findings.

Flag behaviour:

- `--report`: render the full 11-section template from `references/audit-report-template.md` after the verdict callout. The template includes the source-traceability table that justifies preserving `original_prose` in Phase 2.
- `--argdown`: save the `validated_argdown` to `.claude/logic-audits/<slug>.argdown` (creating the directory if needed). The slug is derived from the input filename or, for inline prose, a short summary of the claim.
- `--interactive`: append a footer pointing at the future continuation command:

  > _To continue this argument turn-by-turn, run: `/claudikins-klaus:aron-spar <slug>`_

The default inline output (no `--report`) MUST include a digest sparring block with:

- **3 strongest objections** — the top-3 entries from Phases 5 and 6 ranked by severity. Each entry cites the scheme (or "structural" for Phase 3 findings) and the target argument.
- **Collapsed crux** — the single argument or premise that decides the question. If the conclusion would flip on the resolution of one specific sub-issue, that sub-issue is the crux.
- **Voting issues** — the reasons-to-buy-my-side callout as described in `references/methodology-references.md` §3. Frame these from the auditor's perspective, not the proponent's.

Important: the `mitigate / outweigh / concede-and-redirect` and `dropped-arguments-conceded` LD primitives only fire in multi-turn `aron-spar` sessions, not in single-pass digests. Do not invoke them here.

Verdict-decision worked rules:

- If Phase 3 surfaced any `unsupported_premise` of `high` severity that no Phase 6 fix addresses → at best `Conditional`, never `Sound`.
- If Phase 6 produced at least one surviving rebut whose target is the top-level conclusion → `Defeated`.
- If Phase 6 produced surviving undercuts only (no rebuts) → `Conditional` (the conclusion is not falsified, but the inference to it is weakened; name the inference assumptions).
- **Partial rebuts.** If Phase 6 produced a surviving rebut whose target is a SUB-argument or intermediate conclusion (not the top-level claim directly), AND that sub-argument is load-bearing for the top-level conclusion (its support cannot be reconstructed from the remaining premises), default to `Conditional` — name the surviving sub-argument rebut in the one-sentence summary. If the sub-argument is non-load-bearing (the top-level claim survives on independent grounds), the rebut is recorded in the report but does not change the verdict.
- If Phase 7 (corpus mode) put the top-level claim in `OUT` under the grounded extension → `Defeated`, overriding any per-document `Sound` verdict.
- If the document is too thin to even apply Phase 3 (e.g. one sentence with no premise/conclusion structure) → `Indeterminate` with a note explaining what additional structure would be needed.

**Output:** the final verdict callout, the narration body, the digest sparring block, and (if flagged) any artifact files written under `.claude/logic-audits/`.

---

## Usage Notes

- **Persona-neutral.** This skill provides methodology only. The Aron agent wraps it with chess-grandmaster narration; other agents can load it without inheriting that persona.
- **Modes.**
  - Empty input → interactive prompt (ask for the argument first; do not steelman air).
  - Single file path → audit that file.
  - Inline prose → audit the prose directly.
  - Multiple paths or glob → corpus mode (enables Phase 7).
- **Read-only on inputs.** Never modify the user's source prose. Write only to `.claude/logic-audits/` and only when an output flag (`--report`, `--argdown`) requests it.
- **Cross-session persistence is out of scope.** Sparring continuation re-reads `.claude/logic-audits/<slug>.md` rather than restoring in-memory state. Treat each audit invocation as stateless.
- **Structured data hygiene (R-9).** All structured data flows from `mcp__argdown-mcp__*` responses. Sub-skill prose output is for human consumption only; never parse it back into the audit pipeline. This is the single most important invariant in this skill — violating it produces audits that look structured but rest on hallucinated AST data.
- **Verdict callout is mandatory.** Even in degraded mode (no Argdown plugin), the first line of output is still the verdict blockquote — typically `Indeterminate` if no structural verification was possible.
- **YAML-drop is permanent.** Do not file a bug against the Argdown plugin for the YAML-drop in `export_json`. It is documented behaviour; Phase 4 is designed around it.
