---
name: claudikins-klaus:aron-spar
description: Continue a sparring session against a prior Aron audit. Reads .claude/logic-audits/<slug>.md and produces one continuation turn (steelman drift, fired CQ press, drop/press/collapse marker, voting issues if user requests verdict). Respects 4 termination conditions.
argument-hint: "<audit-slug-or-path>"
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

# /claudikins-klaus:aron-spar — Continue Aron Sparring

Continue a turn-by-turn argument with Aron, picking up from a prior audit.

This command is invoked once per sparring turn. Each invocation reads the prior audit, performs exactly one continuation turn, then exits. Slash commands run one-shot in Claude Code; the user drives the cadence by re-invoking `aron-spar` with the same slug.

## Argument resolution

Parse the first argument (`{{1}}` / `{{target}}`):

| Form                               | Resolution                                                                              |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| Bare slug (e.g. `proposal-q1`)     | Look for `.claude/logic-audits/<slug>.md`                                               |
| Full path (e.g. `./audits/foo.md`) | Use as-is                                                                               |
| Empty                              | Error: "no audit specified; pass slug or path"                                          |
| Path does not exist                | Error: "no prior audit found at <path>; run /claudikins-klaus:aron --interactive first" |

If resolution fails, halt immediately. Do not spawn Aron, do not prompt the user.

## Read the prior audit

Read the resolved audit file end-to-end. Extract:

- **Original input** — the claim, file path, or corpus list from the audit metadata block at the top
- **Verdict callout** — current state (Sound / Conditional / Defeated / Indeterminate)
- **Structural findings** — the list of premises, hidden assumptions, equivocations
- **Fired Critical Questions** — and their status (open / answered / dropped)
- **Sparring trail** — any previous `## Sparring continuation — turn N` blocks appended by prior `/aron-spar` invocations
- **Termination state** — if a `## Sparring closed — <reason>` footer is present, refuse to continue (see edge cases)

Count the existing continuation turns to determine the next turn number `N`.

## User-turn input

Use `AskUserQuestion` to prompt the user for their move this turn. Offer the menu:

- **Respond to a specific CQ** — which one? (list open CQs by id)
- **Concede the crux** — accept Aron's strongest line and close the spar
- **Request the voting issues** — ask Aron for the verdict summary
- **End the spar** (`/end-spar`) — close without verdict
- **Press a different point** — raise a fresh challenge not yet on the board

Capture the user's selected move as `userMove` (free text where the move requires elaboration).

## Termination conditions (four)

After capturing the user's move, evaluate termination. Aron always gets ONE final turn when termination fires — to collapse to crux and surface voting issues — but no further turns are permitted.

| #   | Condition           | Trigger                                                                                                                            |
| --- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **Concede**         | User selects "concede the crux"                                                                                                    |
| 2   | **Verdict request** | User selects "request the voting issues"                                                                                           |
| 3   | **5-turn stall**    | More than 5 turns since the last meaningful movement (count CQs newly answered/dropped, or crux shifts, across the sparring trail) |
| 4   | **`/end-spar`**     | User selects "end the spar"                                                                                                        |

Mark `terminationFired: true|false` and pass the reason into the Aron prompt so Aron knows whether to render the verdict + voting issues this turn.

## Spawn Aron in continuation mode

Use `Task` to spawn the Aron subagent with continuation context:

```typescript
Task({
  subagent_type: "aron",
  description: `Sparring continuation: ${slug} turn ${turnN}`,
  prompt: `
    SPARRING CONTINUATION
    SLUG: ${slug}
    PRIOR AUDIT: ${auditContent}
    SPARRING TRAIL: ${trailContent}
    USER MOVE THIS TURN: ${userMove}
    TURN NUMBER: ${turnN}
    TERMINATION FIRED: ${terminationFired}
    TERMINATION REASON: ${terminationReason}

    Continue the spar. Apply LD primitives (see argument-audit
    skill, references/methodology-references.md §3):
    dropped-arguments-are-conceded, mitigate/outweigh/concede-and-redirect,
    collapse-to-crux, voting-issues.

    Track which CQs the user has answered vs dropped via the sparring trail.
    End this turn with an explicit drop / press / collapse marker.

    If TERMINATION FIRED is true: render the final voting issues, refresh
    the Verdict callout, and do NOT invite another turn. Otherwise: respond
    to the user's move, advance one ply, and stop.
  `,
  context: "fork",
});
```

Aron handles all persona, board narration, and methodology rigour internally. This command provides only the wiring.

## Append the turn to the audit file

After Aron returns, append the continuation to `.claude/logic-audits/<slug>.md`:

```markdown
## Sparring continuation — turn N (YYYY-MM-DD HH:MM)

<Aron's full turn output, including Verdict callout, board snapshot, drop/press/collapse marker>
```

Use the local timezone for the timestamp. Preserve Aron's output verbatim — do not rewrite, summarise, or strip chess flourishes.

If termination fired, also append a close footer:

```markdown
## Sparring closed — <reason>

Closed by: <concede | verdict-request | 5-turn-stall | /end-spar>
Final verdict: <Sound | Conditional | Defeated | Indeterminate>
Turns completed: N
```

This footer is the sentinel for the "refuse to continue closed sessions" edge case below.

## Edge cases

| Case                                                                                | Handling                                                                                                                                                    |
| ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Slug does not resolve to a path AND argument is not a path                          | Error: "no audit specified; pass slug or path" / "no prior audit found at <path>; run /claudikins-klaus:aron --interactive first"                           |
| Audit file exists but is empty or malformed (no Verdict callout, no findings block) | Error: "audit malformed; cannot continue. Re-run /claudikins-klaus:aron --interactive to regenerate"                                                        |
| `## Sparring closed` footer already present                                         | Refuse: "this sparring session is closed; start a new audit with /claudikins-klaus:aron --interactive"                                                      |
| 5-turn stall detected                                                               | Run ONE final turn. Instruct Aron via the prompt to collapse-to-crux and surface voting issues. Append close footer with reason `5-turn-stall`              |
| User selects "request the voting issues"                                            | Aron renders voting issues + refreshed Verdict callout this turn. Append close footer with reason `verdict-request`                                         |
| User selects "concede the crux"                                                     | Aron acknowledges the concession, refreshes Verdict callout to reflect the conceded line, surfaces voting issues. Append close footer with reason `concede` |
| User selects `/end-spar`                                                            | Aron gives a single closing summary (no new attacks). Append close footer with reason `/end-spar`                                                           |
| Argdown plugin not installed                                                        | Aron handles this internally — degraded sparring proceeds without AST traversal. No special handling required at the command layer                          |

## Notes

- This command does NOT carry persona. All Aron-voice (chess board, _tssch_, Karpov asides, Verdict callout discipline) lives in the agent and the skill. The command is wiring only.
- The audit file is the single source of truth for sparring state. Do not maintain a parallel session store. Each `/aron-spar` invocation is stateless apart from what it reads from the file.
- One invocation, one turn, one append. Never loop within a single command run.
