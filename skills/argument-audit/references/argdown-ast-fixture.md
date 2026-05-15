# Argdown `export_json` AST — fixture and field-name reference

This file documents the **actual** JSON AST shape returned by the
`@casualtheorics/argdown-plugin` `export_json` MCP tool. The
`argument-audit` skill (Phase 3 — structural audit) loads this artifact at
runtime so it can traverse the AST using real field names rather than the
description-level names from argdown.org.

- **Captured on:** 2026-05-15
- **Captured by:** Task 1 (`argdown-ast-fixture`)
- **Tool:** `mcp__plugin__@casualtheorics_argdown-plugin__argdown-mcp::export_json`
- **Server bundle:** `/Users/kellen/Projects/agent-argdown/dist/server.js`
- **Round-trip method:** direct JSON-RPC over stdio against the MCP server (the
  tool-executor sandbox was unavailable in this session, but the call still hit
  the real plugin code and returned a real AST — this is **not** degraded).
- **Argdown version (per the plugin's parent `package.json`):** `@argdown/core ^2.0.1`,
  `@argdown/node ^2.0.3`.

## Source fixture

The fixture exercises every construct the audit skill must traverse: a bare
statement definition, a premise–conclusion structure (PCS) with a labelled
inference, support, attack, undercut, and YAML metadata.

```argdown
[Thesis]: Censorship is not wrong in principle. {scheme: "expert-opinion", toulmin: "claim"}
  + <Free-speech-limits>
  - <C1>

<Free-speech-limits>: Free speech ceases when it harms others.

<C1>: Censorship is wrong in principle.

<MyArg>

(1) E is an expert in field D. {toulmin: "data"}
(2) E asserts A. {toulmin: "data"}
-- Modus ponens (uses: 1,2) --
(3) [A]: A is plausibly true. {toulmin: "claim"}
  -> [Thesis]

<Undercutter>: Argument that undercuts MyArg.
  <_ <MyArg>

(1) E has a financial interest in A.
-----
(2) E's testimony is undercut.
```

Notes on the fixture:

- Argdown is whitespace-sensitive at top level; blank lines separate
  sibling definitions. PCS bodies that follow an `<Argument>` header must
  be preceded by a blank line.
- The undercutter is written as a separate `<Undercutter>` argument with a
  child `<_ <MyArg>` relation line; this is the argdown-canonical way to
  express "the conclusion of _Undercutter_ undercuts the inference inside
  _MyArg_".

## Raw AST — verbatim `export_json` output

The MCP tool returns a single text content block. Its body is a short
`Parsed N statements (by source-location), N arguments, N sections.`
header line, the literal token `JSON:`, and then a fenced ```json block.
The JSON block parsed below is reproduced **verbatim** from that block —
no re-formatting beyond `JSON.stringify(parsed, null, 2)`, which preserves
all keys and values.

```json
{
  "arguments": {
    "Free-speech-limits": {
      "type": "argument",
      "relations": [
        {
          "type": "relation",
          "relationType": "support",
          "from": "Free-speech-limits",
          "fromType": "argument",
          "to": "Thesis",
          "toType": "equivalence-class"
        }
      ],
      "members": [
        {
          "type": "statement",
          "role": "argument-description",
          "text": "",
          "startLine": 2,
          "endLine": 2,
          "startColumn": 5,
          "endColumn": 25,
          "isTopLevel": false,
          "title": "Free-speech-limits",
          "isReference": true
        },
        {
          "type": "statement",
          "role": "argument-description",
          "text": "Free speech ceases when it harms others.",
          "startLine": 5,
          "endLine": 5,
          "startColumn": 1,
          "endColumn": 62,
          "isTopLevel": true,
          "title": "Free-speech-limits"
        }
      ],
      "pcs": [],
      "title": "Free-speech-limits"
    },
    "C1": {
      "type": "argument",
      "relations": [
        {
          "type": "relation",
          "relationType": "attack",
          "from": "C1",
          "fromType": "argument",
          "to": "Thesis",
          "toType": "equivalence-class"
        }
      ],
      "members": [
        {
          "type": "statement",
          "role": "argument-description",
          "text": "",
          "startLine": 3,
          "endLine": 3,
          "startColumn": 5,
          "endColumn": 8,
          "isTopLevel": false,
          "title": "C1",
          "isReference": true
        },
        {
          "type": "statement",
          "role": "argument-description",
          "text": "Censorship is wrong in principle.",
          "startLine": 7,
          "endLine": 7,
          "startColumn": 1,
          "endColumn": 39,
          "isTopLevel": true,
          "title": "C1"
        }
      ],
      "pcs": [],
      "title": "C1"
    },
    "MyArg": {
      "type": "argument",
      "relations": [],
      "members": [
        {
          "type": "statement",
          "role": "argument-description",
          "text": "",
          "startLine": 9,
          "endLine": 9,
          "startColumn": 1,
          "endColumn": 7,
          "isTopLevel": true,
          "title": "MyArg",
          "isReference": true,
          "pcs": [
            {
              "type": "statement",
              "text": "E is an expert in field D. ",
              "startLine": 11,
              "startColumn": 5,
              "endLine": 11,
              "endColumn": 49,
              "title": "Untitled 1",
              "role": "premise",
              "argumentTitle": "MyArg"
            },
            {
              "type": "statement",
              "text": "E asserts A. ",
              "startLine": 12,
              "startColumn": 5,
              "endLine": 12,
              "endColumn": 35,
              "title": "Untitled 2",
              "role": "premise",
              "argumentTitle": "MyArg"
            },
            {
              "type": "statement",
              "title": "A",
              "text": "A is plausibly true. ",
              "startLine": 14,
              "startColumn": 5,
              "endLine": 15,
              "endColumn": 13,
              "role": "main-conclusion",
              "argumentTitle": "MyArg",
              "inference": {
                "type": "inference",
                "relations": [],
                "inferenceRules": ["Modus ponens (uses: 1", "2)"],
                "startLine": 13,
                "startColumn": 1,
                "endLine": 13,
                "endColumn": 31,
                "conclusionIndex": 2,
                "argumentTitle": "MyArg"
              }
            }
          ]
        },
        {
          "type": "statement",
          "role": "argument-description",
          "text": "",
          "startLine": 18,
          "endLine": 18,
          "startColumn": 6,
          "endColumn": 12,
          "isTopLevel": false,
          "title": "MyArg",
          "isReference": true
        }
      ],
      "pcs": [
        {
          "type": "statement",
          "text": "E is an expert in field D. ",
          "startLine": 11,
          "startColumn": 5,
          "endLine": 11,
          "endColumn": 49,
          "title": "Untitled 1",
          "role": "premise",
          "argumentTitle": "MyArg"
        },
        {
          "type": "statement",
          "text": "E asserts A. ",
          "startLine": 12,
          "startColumn": 5,
          "endLine": 12,
          "endColumn": 35,
          "title": "Untitled 2",
          "role": "premise",
          "argumentTitle": "MyArg"
        },
        {
          "type": "statement",
          "title": "A",
          "text": "A is plausibly true. ",
          "startLine": 14,
          "startColumn": 5,
          "endLine": 15,
          "endColumn": 13,
          "role": "main-conclusion",
          "argumentTitle": "MyArg",
          "inference": {
            "type": "inference",
            "relations": [],
            "inferenceRules": ["Modus ponens (uses: 1", "2)"],
            "startLine": 13,
            "startColumn": 1,
            "endLine": 13,
            "endColumn": 31,
            "conclusionIndex": 2,
            "argumentTitle": "MyArg"
          }
        }
      ],
      "title": "MyArg",
      "startLine": 11,
      "startColumn": 1,
      "endLine": 15,
      "endColumn": 13
    },
    "Undercutter": {
      "type": "argument",
      "relations": [
        {
          "type": "relation",
          "relationType": "undercut",
          "from": "A",
          "fromType": "equivalence-class",
          "to": "Undercutter",
          "toType": "inference",
          "conclusionIndex": 1
        }
      ],
      "members": [
        {
          "type": "statement",
          "role": "argument-description",
          "text": "Argument that undercuts MyArg.",
          "startLine": 17,
          "endLine": 18,
          "startColumn": 1,
          "endColumn": 12,
          "isTopLevel": true,
          "title": "Undercutter",
          "pcs": [
            {
              "type": "statement",
              "text": "E has a financial interest in A.",
              "startLine": 20,
              "startColumn": 5,
              "endLine": 20,
              "endColumn": 37,
              "title": "Untitled 3",
              "role": "premise",
              "argumentTitle": "Undercutter"
            },
            {
              "type": "statement",
              "text": "E's testimony is undercut.",
              "startLine": 22,
              "startColumn": 5,
              "endLine": 22,
              "endColumn": 31,
              "title": "Untitled 4",
              "role": "main-conclusion",
              "argumentTitle": "Undercutter",
              "inference": {
                "type": "inference",
                "relations": [
                  {
                    "type": "relation",
                    "relationType": "undercut",
                    "from": "A",
                    "fromType": "equivalence-class",
                    "to": "Undercutter",
                    "toType": "inference",
                    "conclusionIndex": 1
                  }
                ],
                "inferenceRules": [],
                "startLine": 21,
                "startColumn": 1,
                "endLine": 21,
                "endColumn": 6,
                "conclusionIndex": 1,
                "argumentTitle": "Undercutter"
              }
            }
          ]
        }
      ],
      "pcs": [
        {
          "type": "statement",
          "text": "E has a financial interest in A.",
          "startLine": 20,
          "startColumn": 5,
          "endLine": 20,
          "endColumn": 37,
          "title": "Untitled 3",
          "role": "premise",
          "argumentTitle": "Undercutter"
        },
        {
          "type": "statement",
          "text": "E's testimony is undercut.",
          "startLine": 22,
          "startColumn": 5,
          "endLine": 22,
          "endColumn": 31,
          "title": "Untitled 4",
          "role": "main-conclusion",
          "argumentTitle": "Undercutter",
          "inference": {
            "type": "inference",
            "relations": [
              {
                "type": "relation",
                "relationType": "undercut",
                "from": "A",
                "fromType": "equivalence-class",
                "to": "Undercutter",
                "toType": "inference",
                "conclusionIndex": 1
              }
            ],
            "inferenceRules": [],
            "startLine": 21,
            "startColumn": 1,
            "endLine": 21,
            "endColumn": 6,
            "conclusionIndex": 1,
            "argumentTitle": "Undercutter"
          }
        }
      ],
      "title": "Undercutter",
      "startLine": 20,
      "startColumn": 1,
      "endLine": 22,
      "endColumn": 31
    }
  },
  "statements": {
    "Thesis": {
      "type": "equivalence-class",
      "title": "Thesis",
      "relations": [
        {
          "type": "relation",
          "relationType": "support",
          "from": "Free-speech-limits",
          "fromType": "argument",
          "to": "Thesis",
          "toType": "equivalence-class"
        },
        {
          "type": "relation",
          "relationType": "attack",
          "from": "C1",
          "fromType": "argument",
          "to": "Thesis",
          "toType": "equivalence-class"
        },
        {
          "type": "relation",
          "relationType": "attack",
          "from": "A",
          "fromType": "equivalence-class",
          "to": "Thesis",
          "toType": "equivalence-class"
        }
      ],
      "members": [
        {
          "type": "statement",
          "role": "top-level-statement",
          "isTopLevel": true,
          "title": "Thesis",
          "text": "Censorship is not wrong in principle. ",
          "startLine": 1,
          "startColumn": 1,
          "endLine": 3,
          "endColumn": 8
        },
        {
          "type": "statement",
          "role": "relation-statement",
          "title": "Thesis",
          "isReference": true,
          "startLine": 15,
          "startColumn": 6,
          "endLine": 15,
          "endColumn": 13
        }
      ],
      "isUsedAsTopLevelStatement": true,
      "isUsedAsRelationStatement": true
    },
    "Untitled 1": {
      "type": "equivalence-class",
      "title": "Untitled 1",
      "relations": [],
      "members": [
        {
          "type": "statement",
          "text": "E is an expert in field D. ",
          "startLine": 11,
          "startColumn": 5,
          "endLine": 11,
          "endColumn": 49,
          "title": "Untitled 1",
          "role": "premise",
          "argumentTitle": "MyArg"
        }
      ],
      "isUsedAsPremise": true
    },
    "Untitled 2": {
      "type": "equivalence-class",
      "title": "Untitled 2",
      "relations": [],
      "members": [
        {
          "type": "statement",
          "text": "E asserts A. ",
          "startLine": 12,
          "startColumn": 5,
          "endLine": 12,
          "endColumn": 35,
          "title": "Untitled 2",
          "role": "premise",
          "argumentTitle": "MyArg"
        }
      ],
      "isUsedAsPremise": true
    },
    "A": {
      "type": "equivalence-class",
      "title": "A",
      "relations": [
        {
          "type": "relation",
          "relationType": "attack",
          "from": "A",
          "fromType": "equivalence-class",
          "to": "Thesis",
          "toType": "equivalence-class"
        },
        {
          "type": "relation",
          "relationType": "undercut",
          "from": "A",
          "fromType": "equivalence-class",
          "to": "Undercutter",
          "toType": "inference",
          "conclusionIndex": 1
        }
      ],
      "members": [
        {
          "type": "statement",
          "title": "A",
          "text": "A is plausibly true. ",
          "startLine": 14,
          "startColumn": 5,
          "endLine": 15,
          "endColumn": 13,
          "role": "main-conclusion",
          "argumentTitle": "MyArg",
          "inference": {
            "type": "inference",
            "relations": [],
            "inferenceRules": ["Modus ponens (uses: 1", "2)"],
            "startLine": 13,
            "startColumn": 1,
            "endLine": 13,
            "endColumn": 31,
            "conclusionIndex": 2,
            "argumentTitle": "MyArg"
          }
        }
      ],
      "isUsedAsIntermediaryConclusion": false,
      "isUsedAsMainConclusion": true
    },
    "Untitled 3": {
      "type": "equivalence-class",
      "title": "Untitled 3",
      "relations": [],
      "members": [
        {
          "type": "statement",
          "text": "E has a financial interest in A.",
          "startLine": 20,
          "startColumn": 5,
          "endLine": 20,
          "endColumn": 37,
          "title": "Untitled 3",
          "role": "premise",
          "argumentTitle": "Undercutter"
        }
      ],
      "isUsedAsPremise": true
    },
    "Untitled 4": {
      "type": "equivalence-class",
      "title": "Untitled 4",
      "relations": [],
      "members": [
        {
          "type": "statement",
          "text": "E's testimony is undercut.",
          "startLine": 22,
          "startColumn": 5,
          "endLine": 22,
          "endColumn": 31,
          "title": "Untitled 4",
          "role": "main-conclusion",
          "argumentTitle": "Undercutter",
          "inference": {
            "type": "inference",
            "relations": [
              {
                "type": "relation",
                "relationType": "undercut",
                "from": "A",
                "fromType": "equivalence-class",
                "to": "Undercutter",
                "toType": "inference",
                "conclusionIndex": 1
              }
            ],
            "inferenceRules": [],
            "startLine": 21,
            "startColumn": 1,
            "endLine": 21,
            "endColumn": 6,
            "conclusionIndex": 1,
            "argumentTitle": "Undercutter"
          }
        }
      ],
      "isUsedAsIntermediaryConclusion": false,
      "isUsedAsMainConclusion": true
    }
  },
  "relations": [
    {
      "type": "relation",
      "relationType": "support",
      "from": "Free-speech-limits",
      "fromType": "argument",
      "to": "Thesis",
      "toType": "equivalence-class"
    },
    {
      "type": "relation",
      "relationType": "attack",
      "from": "C1",
      "fromType": "argument",
      "to": "Thesis",
      "toType": "equivalence-class"
    },
    {
      "type": "relation",
      "relationType": "attack",
      "from": "A",
      "fromType": "equivalence-class",
      "to": "Thesis",
      "toType": "equivalence-class"
    },
    {
      "type": "relation",
      "relationType": "undercut",
      "from": "A",
      "fromType": "equivalence-class",
      "to": "Undercutter",
      "toType": "inference",
      "conclusionIndex": 1
    }
  ],
  "sections": [],
  "tags": {}
}
```

## Field-name reference (what `argument-audit` must traverse)

### Top-level shape

The `export_json` result is an object with exactly these keys:

| Key          | Type                                | Notes                                                                                                  |
| ------------ | ----------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `arguments`  | `Record<string, IArgument>`         | Keyed by argument **title** (the `<Title>` token). Holds every `<Argument>` block.                     |
| `statements` | `Record<string, IEquivalenceClass>` | Keyed by statement title (or `"Untitled N"` for unlabelled premises). One entry per equivalence class. |
| `relations`  | `IRelation[]`                       | Flat array of every relation in the document — the canonical edge list for graph algorithms.           |
| `sections`   | array                               | `# Section` blocks. Empty in this fixture.                                                             |
| `tags`       | object                              | `#tag` index. Empty in this fixture.                                                                   |

### Where do support / attack / undercut relations live?

Three places — and they are **the same relation objects** (not copies of each other; structurally equal):

1. **`response.relations`** — the flat, canonical list. Use this for global graph traversal.
2. **`response.statements[title].relations`** — relations whose endpoints touch this equivalence class.
3. **`response.arguments[title].relations`** — relations whose endpoints touch this argument.

For undercuts that target an _inference_ (not an equivalence class), the relation is **also** mirrored inside the targeted argument's PCS at `arguments[title].pcs[conclusionIndex].inference.relations[]`. So an undercut on `MyArg`'s inference appears in:

- `relations[]` (canonical)
- `statements[fromTitle].relations[]` (the source EC)
- `arguments[targetTitle].relations[]` (the target argument)
- `arguments[targetTitle].pcs[conclusionIndex].inference.relations[]` (the target inference itself)

### Relation source / target field names

| Field             | Meaning                                                                                                                                           |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `type`            | Always the literal string `"relation"`.                                                                                                           |
| `relationType`    | One of `"support"`, `"attack"`, `"undercut"`. (No `"contrary"`, `"contradiction"` etc. in this fixture.)                                          |
| `from`            | **Title** (string) of the source — NOT a numeric ID. For unlabelled premises this is `"Untitled N"`.                                              |
| `fromType`        | One of `"argument"`, `"equivalence-class"`, `"inference"`.                                                                                        |
| `to`              | Title (string) of the target.                                                                                                                     |
| `toType`          | One of `"argument"`, `"equivalence-class"`, `"inference"`.                                                                                        |
| `conclusionIndex` | **Only present when `toType === "inference"`.** 1-indexed pointer into the target argument's PCS, naming which conclusion the inference produced. |

Important: `from`/`to` are **titles, not IDs.** To resolve a relation to its full
node, look up `response.arguments[r.from]` or `response.statements[r.from]`
based on `fromType`.

When the source of an attack or undercut is an argument's main conclusion
(e.g. `[A]` in this fixture), the relation's `fromType` is **`"equivalence-class"`**
(referring to `A`'s equivalence class), **not** `"argument"`. The conclusion's
EC, not the argument that produced it, is treated as the relation's source.

### Statement equivalence (deduplication)

Statements with the same `[Title]` are gathered into an `IEquivalenceClass`
under `response.statements[title]`. Each EC has:

- `type: "equivalence-class"` — discriminator.
- `title` — the EC's stable identifier (string, used as the key in `statements` and as `from`/`to` in relations).
- `members[]` — every concrete `IStatement` occurrence in the source. Includes
  `role` markers like `"top-level-statement"`, `"relation-statement"`,
  `"premise"`, `"main-conclusion"`, `"argument-description"`, plus
  `startLine`/`startColumn`/`endLine`/`endColumn` source locations.
- `relations[]` — the relations touching this EC.
- `isUsedAsTopLevelStatement`, `isUsedAsRelationStatement`,
  `isUsedAsPremise`, `isUsedAsMainConclusion`,
  `isUsedAsIntermediaryConclusion` — boolean flags marking how the EC participates.

Unlabelled premises receive auto-generated titles `"Untitled 1"`, `"Untitled 2"`,
etc. The audit skill should treat these as anonymous ECs that participate in
their argument's PCS only.

### PCS inference shape

A premise–conclusion structure lives at `response.arguments[title].pcs`, an
ordered array of `IStatement` entries with `role` ∈ {`"premise"`,
`"main-conclusion"`, `"intermediary-conclusion"`}.

A conclusion statement carries an `inference` sub-object describing the
reasoning step that produced it:

```jsonc
{
  "role": "main-conclusion",
  "inference": {
    "type": "inference",
    "relations": [],         // relations that target THIS inference (e.g. undercuts)
    "inferenceRules": [...], // free-text labels from `-- Rule --` syntax
    "startLine": 13,
    "startColumn": 1,
    "endLine": 13,
    "endColumn": 31,
    "conclusionIndex": 2,    // 0-indexed position of the conclusion within the PCS
    "argumentTitle": "MyArg"
  }
}
```

- `inferenceRules` is an array of strings tokenised from the
  `-- Rule (metadata) --` line. Note the parser **splits the inference label
  on commas**, so `Modus ponens (uses: 1,2)` becomes
  `["Modus ponens (uses: 1", "2)"]`. Treat the label as a single human-readable
  string by re-joining with `", "`.
- The `inference.relations[]` array carries any relations targeting this
  inference — most importantly **undercuts** (`relationType: "undercut"`,
  `toType: "inference"`).
- `conclusionIndex` inside `inference` is the **0-indexed** position of the
  conclusion in its argument's `pcs[]`. Confusingly, the `conclusionIndex` on a
  relation with `toType === "inference"` appears to be **1-indexed** (the
  fixture has `conclusionIndex: 1` for the only conclusion of `Undercutter`).
  Audit code should not assume the two indices share an origin — always
  resolve via title and verify by source-location comparison if precision matters.

A bare `<Argument>` reference (one that is _only_ declared by name, with its
body defined elsewhere) appears as a member with
`role: "argument-description"`, `text: ""`, and `isReference: true`.

### YAML metadata location — **NOT IN THE AST**

The fixture writes YAML metadata in three places:

- After `[Thesis]: ...` (statement metadata).
- After each premise `(1) ...` (statement metadata).
- After `[A]: A is plausibly true.` (statement metadata).

**None of this metadata appears in the `export_json` output.** A grep across
the AST returns zero hits for `scheme`, `toulmin`, `expert-opinion`, etc. The
`export_json` tool drops YAML metadata before serialisation. There is no
`data` / `meta` / `metadata` field on `IStatement`, `IArgument`, `IRelation`,
or `IEquivalenceClass` in the version of `@argdown/core` shipped with this
plugin.

**Implication for the audit skill:** if argument-scheme tagging or Toulmin
classification matters, the skill must **parse the YAML metadata out of the
raw Argdown source itself** — `export_json` will not deliver it. Track this
as a known limitation in the skill.

## Field-name surprises vs argdown.org docs

This is the catalogue of "what the argdown.org interface descriptions
imply" vs "what the JSON actually contains". Each item burned probe time
during this round-trip; future operators should NOT have to re-discover
them.

1. **Relation endpoints are titles, not IDs.** The docs describe `IRelation`
   in terms of `from`/`to` references but don't make it explicit that these
   are **string titles** (the `[Statement]` or `<Argument>` label) rather
   than synthetic numeric IDs. Resolution requires `fromType`/`toType`
   discrimination plus a lookup in `arguments` or `statements`.

2. **Conclusion attacks have `fromType: "equivalence-class"`, not
   `"argument"`.** When `[A]: ... -> [Thesis]` makes `A`'s equivalence class
   attack `Thesis`, the attack's source is the EC `A`, not the producing
   argument `MyArg`. Code that walks "arguments that attack Thesis" must
   either also walk EC sources or join through
   `statements[A].members[].argumentTitle`.

3. **Undercut targets carry `toType: "inference"` plus a `conclusionIndex`.**
   The docs talk about undercuts as "attacks on inferences" but do not
   spell out that `toType` becomes `"inference"` and that the relation
   gains an extra `conclusionIndex` field naming which conclusion's
   inference is targeted. Inference relations are also mirrored inside
   `arguments[title].pcs[idx].inference.relations[]`.

4. **YAML metadata is silently dropped.** The argdown.org docs describe a
   `data` field on `IStatement`/`IArgument` for YAML metadata, but
   `export_json` (in the shipped `@argdown/core`) emits no `data` field at
   all. Metadata round-trips through `parse` and `dung_extensions` only at
   their own discretion; for `export_json` it does not survive.

5. **`inferenceRules` is comma-split.** A label like `Modus ponens (uses: 1,2)`
   becomes the array `["Modus ponens (uses: 1", "2)"]`. Treat the array
   as "one rule label, naively split on `,`" and re-join when displaying.

6. **The plain text response wraps the JSON in a markdown fence.** The MCP
   text content is `Parsed N statements ...\nJSON:\n\`\`\`json\n{...}\n\`\`\``.
Callers must extract the fenced block (or attempt direct `JSON.parse` and
   fall back). The "Parsed N statements" header is human-readable, not part
   of the AST.

7. **Untitled premises get auto-generated EC titles.** Bare `(1) E is an expert...`
   premises become equivalence classes named `"Untitled 1"`, `"Untitled 2"`,
   etc., counted across the whole document (not per-argument). Code that
   assumes `statements` is keyed only by author-provided titles will trip
   over these.

8. **An argument's inline-PCS member appears twice.** When you write
   `<MyArg>` followed (after a blank line) by a PCS, the AST stores the
   PCS body **both** under `arguments.MyArg.pcs` and also under
   `arguments.MyArg.members[0].pcs`. Two pointers to the same array of
   statement nodes. Walk one or the other, not both, when computing
   per-argument counts.

9. **Top-level keys are exactly `arguments`, `statements`, `relations`,
   `sections`, `tags`.** No `meta`, no `version`, no `source`, no
   `diagnostics`. The MCP tool's `summary` and `diagnostics` from the
   `IArgdownResponse` wrapper are NOT part of `export_json`'s emitted
   payload — they live only in the human-readable header line that
   precedes the fenced JSON block.
