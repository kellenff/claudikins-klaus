# Expected findings: corpus-contradiction (a.md + b.md)

## About this fixture

This fixture exercises corpus mode: two prose documents in the same audit
batch take opposing stances on a single shared claim. Document A _asserts_
the claim as a finding from a holdout test and uses it to argue for a
roadmap reallocation. Document B _attacks_ the claim by quoting it
verbatim and arguing that the holdout design did not support it. Under v1
detection (literal-text matching, not semantic equivalence), the shared
claim must appear word-for-word in both documents for the contradiction
to register. It does.

The expected behaviour of an auditor running in corpus mode is to surface
the contradiction, identify each document's stance, and report the
grounded-extension partition over the union of arguments under Dung's
abstract argumentation framework. Because B attacks A's load-bearing
claim and A does not attack back, B's argument is IN, A's argument is
OUT under the grounded extension.

## Shared claim (verbatim)

> Reducing API latency below 100ms produces no measurable conversion
> improvement.

This sentence appears verbatim in both `a.md` (as A's headline finding)
and `b.md` (as the quoted target of B's attack).

## Expected findings (most obvious first)

1. **What:** A shared claim appears verbatim in both documents in the
   batch, with opposing stances.
   **Where:** `a.md` paragraph two ("Reducing API latency below 100ms
   produces no measurable conversion improvement."), and `b.md` paragraph
   two (presenting the same sentence as the headline claim it then attacks).
   **Why it matters:** This is the canonical corpus-mode contradiction
   trigger. v1 detection relies on literal-text equivalence; the auditor
   must surface this as a corpus-level finding rather than treating each
   document in isolation.

2. **What:** Document A's stance on the shared claim is **asserts**.
   **Where:** `a.md` presents the sentence as a finding from the holdout
   test and uses it as a premise for the roadmap recommendation in
   paragraph three.
   **Why it matters:** A's whole argument depends on the claim being true;
   it is the load-bearing premise.

3. **What:** Document B's stance on the shared claim is **attacks**.
   **Where:** `b.md` paragraph two explicitly states "I do not believe
   the holdout supports that claim" and the rest of the document develops
   a methodological objection (selection effect on returning users).
   **Why it matters:** B does not contest A's data; B contests A's
   inference from data to claim. The attack is on the inferential link,
   which is the right level for the grounded-extension calculation.

4. **What:** Expected grounded-extension partition over the union of the
   two arguments.
   **Where:** Across the batch.
   **Why it matters:** B attacks A, A does not attack B. Under the
   grounded semantics:
   - **IN:** B (the attacker on the inferential link)
   - **OUT:** A (whose load-bearing claim is attacked)
   - **UNDEC:** none
     The auditor's corpus-mode output should reflect this partition or
     flag a discrepancy with it.

5. **What:** v1 detection caveat — the match is literal text, not
   semantic equivalence.
   **Where:** Methodological note for the auditor and any reviewer of the
   fixture.
   **Why it matters:** A paraphrase ("sub-100ms latency does not improve
   conversion") would _not_ trigger the v1 detector, even though the
   semantic content is the same. The fixture is constructed to satisfy
   the literal-match constraint precisely because v1 cannot be relied on
   for paraphrase detection. Future iterations that add semantic matching
   should re-validate against this fixture and against paraphrase
   variants.
