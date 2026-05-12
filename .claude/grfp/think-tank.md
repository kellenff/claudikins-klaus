# Think Tank: Exemplar README Research

Stage 3 of GRFP — completed 2026-05-11

---

## Methodology

Analyzed READMEs across four categories relevant to claudikins-klaus:

1. Character-driven / personality-first developer tools
2. Snarky/humor-forward CLI tools
3. Debugging tools
4. Claude Code plugins and AI assistant extensions

---

## Case Study 1: thefuck (nvbn/thefuck)

**The gold standard for personality-first developer tool READMEs.**

### What it does

Corrects your previous console command when you type `fuck`.

### README approach

- **Opens with an animated GIF** — the first thing you see is the tool working. No description needed.
- **One-line description underneath:** "Magnificent app which corrects your previous console command."
- **Personality in the description** ("Magnificent") without sacrificing clarity
- **Installation immediately after the hook** — within the first scroll
- **Usage examples are concrete and funny** (the actual commands shown include typos you recognize)
- **Rules listed** — shows depth without overwhelming
- **Requirements + installation** — multiple install paths clearly labeled

### What works

- **GIF as proof**: No amount of description beats seeing it work in 3 seconds
- **The word "Magnificent"**: One personality word in the right place is enough to signal the tone
- **Hook → proof → install → usage → depth**: Structure serves the impatient reader first, the curious reader second
- **Self-referential naming**: The tool IS the joke. README doesn't have to carry all the comedy.

### Lesson for Klaus

Klaus needs a **session demonstration** with the same function as the thefuck GIF — something that shows what the experience actually IS before you explain it. An ASCII session excerpt in a code block could serve this role perfectly.

---

## Case Study 2: git-blame-someone-else

**The README that IS entirely the product.**

### What it does

Reassigns git blame to someone else.

### README approach

- **Entire premise is the joke** — README commits fully to the bit
- **Short** — the joke doesn't need to be long
- **Installation** — still there, just brief
- **"What is this" is answered in one sentence** before diving into character

### What works

- **Committed absurdism works when the premise is watertight.** The joke is so specific that the README CAN be the experience.
- **Brevity preserves the bit.** If it were long, the joke would collapse.

### What doesn't translate to Klaus

- Klaus is more complex than a one-joke tool. The debugging methodology is real and substantial. Klaus needs more real estate to demonstrate value.
- Commitment to the bit at this level works when the tool IS the bit. Klaus has both a bit AND a real methodology. The README must carry both.

---

## Case Study 3: cowsay / lolcat / sl

**Classic minimal novelty tool READMEs.**

### Pattern

- Tiny README
- Name + one-line description
- One ASCII or image showing the output
- Install command
- Done

### What works

- Works for single-purpose novelty tools
- The output IS the documentation

### What doesn't translate to Klaus

- Klaus isn't a novelty. It has a real 8-phase debugging methodology underneath. The minimal approach would undersell it.
- These tools need no explanation because using them is self-evident. Klaus requires context: you have to understand the situation (stuck at 3 AM, debugging in circles) to understand why you'd summon it.

---

## Case Study 4: htop / lazygit / bat (sharkdp)

**Excellent utility READMEs with personality-neutral but excellent visual proof.**

### Pattern (especially bat by sharkdp)

- **Screenshot/GIF showing the output** — immediately, above the fold
- **Short tagline** describing the value proposition
- **Feature list** with icons or checkmarks (scannable)
- **Installation** — multiple package managers, clearly labeled
- **Usage** — the flags/options that matter
- **Clean hierarchy**

### What works

- **Visual proof first** is universal best practice
- **Feature scannability** — bullet points with bold key terms
- **Multiple install paths** reduce friction

### Lesson for Klaus

The feature list format (bullet with icon + **bold term** + explanation) translates well even in Klaus voice. Klaus can describe his methodology phases this way.

---

## Case Study 5: README-driven projects with strong voice

### commitlint / semantic-release

These are in the "corporate professional" mode — excellent, but zero personality. The contrast is instructive: these tools work despite no personality because they solve a known problem people actively search for. Klaus works BECAUSE of personality — the personality is the UX.

### fuck-it (npm package)

A small npm package with a one-joke premise, written with full commitment to the bit. Works at small scale. The README is short and funny. Installation is present.

### Honk (humorous Go libraries)

Go has a tradition of humorous library names with straight-faced READMEs. The humor comes from the name; the README is normal documentation. This approach deliberately separates the joke from the substance.

### Lesson for Klaus

Klaus shouldn't be "funny name, straight README" (like honk-style Go libs). The character and the tool are inseparable — the README needs to maintain the voice. But it can be Klaus-voiced AND structured like a great utility README simultaneously.

---

## Patterns Extracted: What Makes Them Excellent

### 1. The Demo Hook (Universal, Non-Negotiable)

Every great personality tool README shows the tool in action within the first screenful. For GUI tools: screenshot or GIF. For terminal tools: code block showing actual input/output. For Klaus: **a short sample session** showing Klaus responding to a debug request.

### 2. The One-Sentence Value Clarity

Even fully in-character READMEs have a moment where they say, plainly, what the thing does. It can be voiced by the character. It must exist.

Examples:

- thefuck: "Magnificent app which corrects your previous console command."
- Klaus equivalent: "Zis is a Claude Code plugin zat summons ME — Klaus — to debug your catastrophic code vhilst I play an imaginary Pong match against you."

The current Klaus README actually has this. It's buried under "Vhy Are You Still Here?" — good. Could be surfaced slightly faster.

### 3. Installation Within Reach

Every exemplar has installation commands findable within 2-3 scrolls maximum. The exact location varies; findability does not.

### 4. Personality Consistency Without Sacrificing Clarity

The best personality READMEs never let the character confuse the information. Klaus can be hostile, but the hostility can't make installation unclear. thefuck's "Magnificent" signals tone; the actual instructions are plain.

The technique: **character voice in framing, plain information in the content itself.** "You vant to install me? FINE. Here is how." — then the command.

### 5. Scannable Sections

Even personality READMEs use headers. The headers can be in-character. The content under them should be findable by someone scrolling looking for a specific thing.

Current Klaus has good sections. Some headers could be tightened for scannability.

### 6. The Exit / Closing Moment

Great personality READMEs often have a closing that reinforces the character. Klaus already has this (the exit sequence). It's one of the strongest elements of the current README.

---

## What Great Claude Code Plugin READMEs Do

Claude Code's plugin ecosystem is young. Most plugin READMEs are functional but not exemplary. The patterns that work:

- **What the command is** (the slash command name, prominent)
- **What it does in one sentence**
- **Prerequisites** (Claude Code, any required setup)
- **Usage examples** with actual command syntax
- **What you get** (output format, behavior)

Klaus already covers most of these. The missing element: **a real usage example** showing what happens when you actually run `/klaus`.

---

## The Anti-Patterns to Avoid

### The Comedy Trap

Going so deep on the character that the README stops being functional documentation. Some READMEs are so committed to the bit that you can't find the installation instructions without reading everything. This is a novelty that wears off on second visit.

### The Personality Promise / Utility Letdown

READMEs that are funny but the tool isn't actually useful. Klaus escapes this because the debugging methodology is real. The README must make this clear — not by breaking character, but by letting the methodology surface through the character.

### The Long Introduction Before the Point

Burying what the tool does under three paragraphs of scene-setting. The current Klaus README walks this line. "GO AVAY" is bold but it delays the value proposition. The fix: one more sentence of plain description earlier.

### Badge Overload

Too many GitHub badges break the visual rhythm and look like they're compensating for lack of substance. Klaus currently has one badge (Pong Champion). This is perfect.

---

## Structural Template from Best Practices

Based on exemplar analysis, the optimal structure for Klaus:

```
[VISUAL ANCHOR] — ASCII Pong court (already exists, excellent)
[CHARACTER BADGE] — Pong Champion badge (already exists)
[THE BIT OPENING] — Klaus's hostile greeting (already exists)
[ONE-SENTENCE CLARITY] — "Zis is a plugin zat..." (exists but could surface faster)
[THE DEMO] — Sample session in code block (MISSING — most critical gap)
[INSTALLATION] — "Ze Summoning Ritual" (exists, good name)
[COMMANDS] — Usage examples (exists)
[THE METHODOLOGY] — 8-phase table (exists, good)
[TERMINOLOGY] — Translation table (exists, excellent)
[THE VOCABULARY] — German insults (exists, excellent)
[FUTURE] — Tease Ze Ball subagent (partially exists)
[THE EXIT] — Leaving sequence (exists, strong)
[LICENSE] — "MIT. Take it. I do not care." (exists, perfect)
```

The one structural addition that would transform the README: **the demo session block**, placed after the one-sentence clarity statement and before installation.

---

## Summary: The Three Moves

1. **Add the demo session** — 8-12 lines showing Klaus receiving a bug report, responding with ASCII court + pong commentary + one tool call + one fix + score update. This is the thefuck GIF equivalent.

2. **Surface the one-sentence clarity slightly earlier** — it exists ("Zis is a Claude Code plugin...") but arrives after some theatre. Move it one paragraph earlier, or make it the subheader under the ASCII art.

3. **Keep everything else** — the current README is genuinely good. The character is committed and consistent. The sections are well-named. The exit sequence is excellent. The vocabulary and terminology tables are memorable. This needs addition, not restructuring.

---

_Next: Stage 4 — Brain Jam (`/claudikins-grfp:brain-jam`)_
