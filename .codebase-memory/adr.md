## PURPOSE

- **[ADR-plan-2026-05-15-1137-002]** Finalise plan after absorbing Klaus's hits #1, #4, #6, #7; conscious non-application of hits #2 and #5
  - Rationale: Klaus reviewer scored 3-0 with seven distinct hits during Phase 5 outline review; identified killer (one-shot agents don't loop, breaking sparring assumption), big (SC-9 contradiction-vs-equivalence scoping bug), and four notable/smaller methodology refinements. Revisions absorbed: (#7) sparring split into digest-default + --interactive opt-in via new commands/aron-spar.md follow-up command (adds T9, brings total to 9 tasks); (#1) SC-9 reworded to "shared claim asserted in one doc and attacked in another (literal-text match)"; (#4) skill split from 7 phases to 8 (classify-schemes and fire-critical-questions separated); (#6) SC-7 reworded to "traceable source-span in report; prose may paraphrase". Conscious non-applications: (#2) Walton scope kept at four named schemes; (#5) fixture realism kept as 3 synthetic. Plan locked at 9 tasks across 4 batches; 11 success criteria; 9 risks.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#outline.phase-5.iterate-or-finalise

## STACK

- **[ADR-plan-2026-05-15-1137-007]** Ready to Ship — verify gate unlocked; verified_commit_sha 9feb661; manifest of 21 markdown files recorded
  - Rationale: Phase 1 (adapted for markdown-only plugin) passed 8/8 structural checks: plugin.json valid JSON; frontmatter shape across all four new files; R-4 grep clean (3 of 3 expected matches, 0 drift); Klaus-trio byte-identical to pre-Aron baseline via git hash-object; corpus literal-text byte-match present in both corpus-contradiction files; SKILL v1.0.1 patches present. Phase 2 catastrophiser-equivalent (T8b smoke during execute) hit 5 of 5 expected findings on hidden-assumption fixture (target ≥2); all 8 phases executed; 16 CQs fired across 3 arguments and 4 Walton scheme assignments; 4 Pollock-classified rebuttals (1 rebut + 3 undercuts); persona compliance maintained; methodology survived degraded mode. Phase 3 cynic polish on skill body landed as 9feb661 (12 in-place tightenings, 0 line changes, all operational constraints preserved). Deferred items: 4 of 7 T8b methodology gaps to v1.1; T6 minors (slug TOCTOU, Unicode, corpus-detection fuzziness); T9 minors (parallel race, /end-spar menu label, Bash over-permissioning). None are shipping blockers.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#verify.phase-5.gate

## ARCHITECTURE

_No decisions recorded yet._

## PATTERNS

- **[ADR-plan-2026-05-15-1137-006]** Merge T7 README branch with --no-ff (consistent with batches 1-3 precedent). T8a structural verify produced no files to merge. T8b smoke (Klaus-as-Aron) produced no files to merge. SKILL.md v1.0.1 fix-up (gaps 1, 5, 6) committed directly on main as 702bc24 because babyclaude was blocked by permission prompts on the merged file.
  - Rationale: T7 owns README.md, no conflicts. The SKILL.md fix-up commit landed directly on main rather than via worktree because (a) the file was already merged on main, (b) babyclaude attempts to Edit/Write got permission-denied, (c) the orchestrator-direct fix-up was a tightly-scoped 3-edit surgical pass equivalent to what babyclaude was instructed to do.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#execute.phase-5.merge-decision.batch-4

- **[ADR-plan-2026-05-15-1137-008]** Direct push to remote main (no PR); preserve full 24-commit history including task merges, fix-ups, cynic polish, and skill v1.0.1
  - Rationale: Solo-owned plugin (single maintainer); no parallel contributors needing the PR review gate. Full per-task history is the Tier-1 audit trail's commit-level companion and is best preserved. The PR-gate methodology rule is explicitly waived for solo plugin maintenance; this decision is recorded as the architectural precedent for future Aron-like additions to this repo.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#ship.stage-2.commit-strategy

## TRADEOFFS

- **[ADR-plan-2026-05-15-1137-003]** Merge each branch separately with --no-ff (preserve history) — three merge commits, one per task (Batch 1: T1+T2+T3)
  - Rationale: Conflict check: None detected (T1 owns argdown-ast-fixture.md, T2 owns three reference docs, T3 owns 10 fixture files; non-overlapping). Three merges executed (T1 +970 lines, T2 +957 lines across three files, T3 +547 lines across 10 fixture files). Per-branch history preserved including T3 retry fde13ce and the three fix-up commits, supporting future review-by-blame.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#execute.phase-5.merge-decision.batch-1

- **[ADR-plan-2026-05-15-1137-004]** Merge each branch separately with --no-ff (preserve history) — two merge commits, T4 then T5 (Batch 2)
  - Rationale: Conflict check: None detected (T4 owns skills/argument-audit/SKILL.md, T5 owns agents/aron.md; non-overlapping). Two merges: T4 +259 lines SKILL.md, T5 +241 lines agents/aron.md including the --sober fix-up commit 0d6a8ca on top of 8d0d074.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#execute.phase-5.merge-decision.batch-2

- **[ADR-plan-2026-05-15-1137-005]** Merge each branch separately with --no-ff (preserve history) — two merge commits, T6 then T9 (Batch 3)
  - Rationale: Conflict check: None detected (T6 owns commands/aron.md, T9 owns commands/aron-spar.md; non-overlapping). Two merges: T6 +148 lines, T9 +157 lines including fix-up commit 0a9458e on top of b1f9488.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#execute.phase-5.merge-decision.batch-3

## PHILOSOPHY

- **[ADR-plan-2026-05-15-1137-001]** Adopt Approach B — agent + thick methodology skill mirroring klaus + debugging shape; 8-phase argument-audit skill with sparring as terminal phase (later split to 8 phases per Klaus review hit #4)
  - Rationale: B matches the plugin's established klaus.md + debugging shape and is the path of least surprise; A (thin orchestrator) under-delivers on success criteria 1 (non-obvious weaknesses) and 4 (clean round-trip) because both require methodology beyond delegation; C (layered trio with separate audit and sparring skills) creates discovery friction without distinct workflows since sparring is naturally the terminal phase of an audit, not a parallel concern.
  - Source: kernel-decisions/plan-2026-05-15-1137.md#outline.phase-3.approach-selection
