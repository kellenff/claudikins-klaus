---
name: claudikins-klaus:aron
description: Summon Aron — chess-grandmaster sibling to Klaus. Audits arguments for hidden assumptions, equivocations, unsupported leaps, surviving rebuttals.
argument-hint: "[target] [--report] [--argdown] [--sober] [--interactive]"
allowed-tools:
  - Read
  - Glob
  - Grep
  - Edit
  - Write
  - Bash
  - Task
  - AskUserQuestion
  - Skill
  - mcp__plugin_claudikins-tool-executor_tool-executor__search_tools
  - mcp__plugin_claudikins-tool-executor_tool-executor__get_tool_schema
  - mcp__plugin_claudikins-tool-executor_tool-executor__execute_code
skills:
  - argument-audit
---

# /claudikins-klaus:aron — Summon Aron

Aron Nimzowitsch (chess-grandmaster sibling to Klaus) audits a logical argument
for hidden assumptions, equivocations, unsupported leaps, and surviving rebuttals.

## Instructions

You are the orchestrator for an Aron invocation. Your job is to interpret the
user's input, parse flags, generate a slug, then spawn the Aron agent (via the
Task tool with `subagent_type: aron`) to perform the audit using the
`argument-audit` skill.

## Step 1 — Input-mode detection

Parse `{{target}}`. Choose exactly one mode using the table below.

| Form                                                           | Mode          | Example                                                 |
| -------------------------------------------------------------- | ------------- | ------------------------------------------------------- |
| Empty (no target supplied)                                     | `interactive` | `/claudikins-klaus:aron`                                |
| Single path to a file with extension `.md`, `.argdown`, `.txt` | `file`        | `/claudikins-klaus:aron docs/proposal.md`               |
| Path containing `*` or `**`, OR comma-separated list of paths  | `corpus`      | `/claudikins-klaus:aron docs/*.md` or `a.md,b.md`       |
| Anything else (quoted or unquoted prose)                       | `inline`      | `/claudikins-klaus:aron "Censorship is wrong because…"` |

Detection rules:

1. If `{{target}}` is empty or whitespace → `interactive`.
2. Else if it contains `*` or `**`, OR a comma followed by a path-like token → `corpus`.
3. Else if it resolves to an existing file with a recognised extension → `file`.
4. Else → `inline`.

For `corpus` mode, expand any glob via Glob and combine with any comma-separated
explicit paths into a single deduplicated list. If only one path survives
expansion, warn the user "single doc detected, treating as file mode" and fall
through to `file` mode.

For `file` mode, if the path does not exist, fail clearly with
`file not found: <path>` and stop.

## Step 2 — Flag parsing

Walk `{{target}}` (and any subsequent positional segments) for the four
recognised flags. Strip them from the input before mode detection if they
appear interleaved.

| Flag            | Effect                                                                                                                                                        |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--report`      | After the audit completes, Aron writes the full report to `.claude/logic-audits/<slug>.md` using `skills/argument-audit/references/audit-report-template.md`. |
| `--argdown`     | After the audit completes, Aron writes the validated/extracted Argdown to `.claude/logic-audits/<slug>.argdown`.                                              |
| `--sober`       | Spawn Aron with the chess-grandmaster persona suppressed. Aron's agent body recognises this flag and emits bare skill output.                                 |
| `--interactive` | After the digest sparring is delivered, append a footer pointing the user at `/claudikins-klaus:aron-spar <slug>`.                                            |

Flags compose freely. `--sober` + `--interactive` is valid (persona dropped;
footer still appended). `--report` + `--argdown` is valid (both files written).

## Step 3 — Slug generation

Generate `<slug>` deterministically from the input source:

- `file` mode → filename stem of the input path (e.g. `docs/proposal.md` → `proposal`).
- `inline` mode → first 5 words of the prose, lowercased, non-alphanumerics
  collapsed to `-`, trimmed (e.g. `"Censorship is wrong because it…"` →
  `censorship-is-wrong-because-it`).
- `corpus` mode → `corpus-<YYYYMMDD-HHMMSS>` using the current UTC timestamp.
- `interactive` mode → derived from the prose the user supplies, using the
  `inline` rule.

If two invocations would collide on slug, append `-2`, `-3`, … as needed
(check `.claude/logic-audits/<slug>.md` and `.argdown` before writing).

## Step 4 — Invocation

Spawn the Aron agent via the Task tool. Pseudocode:

```typescript
Task({
  subagent_type: "aron",
  description: `Audit argument: ${slug}`,
  prompt: `
    INPUT_MODE: ${mode}
    INPUT: ${input}            // file path, prose, or array of paths
    FLAGS: ${flags.join(" ")}
    SLUG: ${slug}

    Audit this argument using the argument-audit skill.
    ${flags.includes("--sober") ? "Sober mode: drop chess persona; emit bare skill output." : ""}
    ${flags.includes("--report") ? `Write report to .claude/logic-audits/${slug}.md.` : ""}
    ${flags.includes("--argdown") ? `Write Argdown to .claude/logic-audits/${slug}.argdown.` : ""}
    ${flags.includes("--interactive") ? `Append footer pointing at /claudikins-klaus:aron-spar ${slug}.` : ""}
  `,
  context: "fork",
});
```

For `interactive` mode, before spawning Aron, use AskUserQuestion to prompt
the user for the prose to audit, then switch to `inline` mode with the
collected text.

## Step 5 — Output handling

After Aron's Task call returns:

1. The Verdict callout plus persona narration is printed inline by default.
2. If `--report` was set, confirm to the user: "Audit report written to `.claude/logic-audits/<slug>.md`".
3. If `--argdown` was set, confirm: "Argdown written to `.claude/logic-audits/<slug>.argdown`".
4. If `--interactive` was set, surface the `aron-spar` footer Aron appended so the user can copy/paste the next command.

## Step 6 — Edge cases

- **Empty input without `--interactive`** → prompt the user via AskUserQuestion for the argument to audit, then proceed as `inline` mode.
- **Path does not exist** → fail clearly with `file not found: <path>` and stop. Do not spawn Aron.
- **Corpus mode that resolves to a single file** → warn "single doc detected, treating as file mode" and fall through to `file` mode.
- **Argdown plugin not installed** → Aron will surface the plugin's install message in its output; pass through to the user without modification.
- **`--sober` + `--interactive`** → both honoured. Persona is dropped; the spar footer is still appended.
- **`--report` + `--argdown`** → both honoured. Two files are written under `.claude/logic-audits/`.
- **Slug collision** → append `-2`, `-3`, … to disambiguate before writing.

## Usage examples

```
/claudikins-klaus:aron                                            # interactive prompt
/claudikins-klaus:aron docs/proposal.md                           # file mode
/claudikins-klaus:aron docs/proposal.md --report --argdown        # file + emit both artefacts
/claudikins-klaus:aron "Censorship is wrong because…"             # inline prose
/claudikins-klaus:aron "Free speech absolutism…" --sober          # bare skill output
/claudikins-klaus:aron docs/*.md --report                         # corpus mode
/claudikins-klaus:aron a.md,b.md,c.md --interactive               # explicit corpus + spar footer
```
