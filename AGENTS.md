# Teleprompter — Agent Guide

Single-file HTML teleprompter at `C:\xampp\htdocs\!PROJECTBANK\teleprompter\index.html`. Used by Listy for English-speaking practice and content drafting (eventual IG/TikTok). When Listy asks for a "speaking script", "teleprompter script", or "script for the teleprompter", produce content using the cue grammar below.

## Two modes

- **Scroll** — continuous auto-scroll text with cue-styled inline runs
- **Karaoke** — one source-line at a time, words highlight progressively at wpm pace, TikTok-style read-along (preferred mode currently)

Same source renders in both. Lines are the atomic unit — each non-empty line in source = one karaoke card. Empty lines are skipped.

## Cue grammar

Inline tags `{tag}…{/}`. Closing tag is universal `{/}`. Tags can nest: `{slow}{emp}word{/}{/}`. Multipliers stack.

| Tag | Visual | Karaoke dwell | When to use |
|---|---|---|---|
| `{loud}` | bold orange, larger | 1.25× | command moments, declarative key points |
| `{soft}` | small gray | 0.85× | parenthetical asides, dropping energy |
| `{whisper}` | italic dotted | 1.15× | secrets, intimate reveals, "between you and me" |
| `{slow}` | wide letters, blue underline | 1.6× | weighty / complex points that need to land |
| `{fast}` | tight italic, orange | 0.6× | excitement, list-of-things, throwaway context |
| `{emp}` | yellow highlight | 1.4× | the specific word/number the audience must remember |
| `{urg}` | red bold | 1.1× | stakes, danger, problem framing |
| `{warm}` | warm pink | 1.1× | empathy, vulnerability, self-disclosure |
| `{up}` | ↑ marker | 1.0× | single rising inflection (one word) |
| `{down}` | ↓ marker | 1.0× | single falling inflection |
| `{rise}` | each word lifts visually higher | 1.0× | multi-word ascending pitch contour |
| `{fall}` | each word drops visually lower | 1.0× | multi-word descending pitch contour |
| `{hit}` | snap-scale animation | 1.5× | punch beat — single word impact, the "hero shot" |
| `{stretch}` | letter-spacing expands | 2.0× | elongated word ("veerry"), drawing it out |

Special:
- `{breath}` on its own line = breath mark (visual reminder, no timing effect)
- `{pause:N}` — **inline word-level pause** in karaoke. Place between words. Karaoke freezes for N seconds before advancing; the word just before the pause stays held in blue so the reader doesn't lose their place. Invisible in DOM (zero size). Standalone-line pauses are skipped — pauses must be inline within a real line.

  Use sparingly: `So the answer is{pause:0.4} {hit}forty-four hundred.{/}` — the pause builds anticipation for the hit. Don't pad every line; reserve for setups before key reveals.

## Script-writing principles

**1. Density.** 1–2 cues per sentence is the target. Heavy stacking creates noise. The example script bundled in `index.html` is intentionally dense for technique drilling — real performance scripts should be lighter.

**2. Match cue to function:**
- Specific number / proper noun the audience must remember → `{emp}`
- Biggest-impact word in a sentence → `{hit}`
- Whole phrase building tension → `{rise}`, often resolving into `{hit}` or `{loud}`
- Stakes / problem framing → `{urg}`
- Vulnerability or self-disclosure → `{warm}` or `{soft}`

**3. Lines = breaths.** Each source line becomes one karaoke card and one natural breath in delivery. Break lines where you'd actually pause to breathe. Don't write 40-word run-on lines — they wrap awkwardly in karaoke.

**4. The opening line matters.** First card sets tone. Lead with `{warm}` or `{up}` for hook energy, not `{slow}` (which feels heavy on a cold start).

**5. Ration `{hit}`.** Max 3–4 per script. It's the hero shot. Overuse kills impact.

**6. Stack at most 2 cues.** `{slow}{emp}word{/}{/}` is fine. `{slow}{loud}{emp}{hit}word{/}{/}{/}{/}` is incoherent and the timing multiplier (≈3.4×) breaks pacing.

**7. Operator content > generic motivation.** Listy's edge is real numbers and real ops experience. Default topic shape: "here's a specific number from my business → here's what most people don't see → here's the lesson." Do not write generic "hustle / mindset / believe in yourself" content — that's not him.

## Example structure (annotated)

```
{warm}Hook line — empathy or curiosity, not a hard sell.{/}

{slow}Set the stage with one concrete fact.{/} The {emp}specific number{/} they must remember.

{up}A question that turns the camera on the audience —{/} {hit}the punch word.{/}

[Body: 2-4 cards developing the insight, with `{emp}` on key terms and one `{rise}` building toward a payoff.]

{rise}Build building building{/} — {hit}{loud}payoff word.{/}{/}

{warm}One-line lesson or close.{/}
```

## Where scripts live (READ THIS before creating one)

Scripts are now external files. The embedded `<script type="text/teleprompter" id="src">` block in `index.html` is **only the fallback default** — never put new scripts there.

When Listy asks for a new script:

1. Create a file at `scripts/<kebab-case-name>.txt`. Plain UTF-8, raw cue source, no front-matter, no JSON wrapper.
2. **Append a manifest entry** in `scripts/index.json`:
   ```json
   { "file": "your-script.txt", "title": "Short Human Title" }
   ```
   The dropdown only shows what's registered. Forgetting this step means the script is invisible in the UI.
3. Filename rules: lowercase-kebab-case, English (proper nouns OK), topic-prefixed when helpful (`lalafun-…`, `floural-…`, `personal-brand-…`). No spaces, no `.md`, no nested folders — the loader only fetches `scripts/<file>` flat.
4. Don't mutate someone else's existing script file. Copy to a new filename and register it.
5. End the README's user-facing rules and these AGENTS.md rules in sync — if you change the workflow, update both.

The Edit Script modal in the UI is for ephemeral local edits only; it writes to `localStorage`, not to disk. If a draft is worth keeping, save it as a file in `scripts/` and register it.

## Architecture (when editing index.html)

- Parser is in the `<script>` block at the bottom.
- `TAGS` array — all supported cue names. Add new cues here.
- `CUE_MULT` — karaoke timing multiplier per cue. `1.0` = no timing change, just visual.
- `transform(line)` — recursive cue-tag → `<span class="c-tag">…</span>` substitution.
- `parse(src)` — scroll mode rendering. Splits source by lines, transforms each.
- `buildCard(line)` — karaoke mode. After `transform()`, walks text nodes, wraps each whitespace-separated word in `<span class="kw">`, then computes per-word multiplier by walking up cue ancestors, and applies pitch-contour offsets for `{rise}`/`{fall}`.
- `karaokeTick(dt)` — advances `wordIdx` based on baseDur × current word's multiplier.
- Mode toggle just adds/removes `mode-karaoke` class on `<body>`. CSS handles visibility.
- Script source resolution: dropdown selection → `localStorage` key `teleprompter.script.<file>` (per-script edit cache) → `fetch('scripts/<file>')` → embedded `#src` fallback. Manifest at `scripts/index.json`. Selected file persisted at `localStorage` key `teleprompter.selected`. Legacy single-key `teleprompter.script` is still honored when the `(default — embedded)` option is active.
- "↻ Reset to file" button in the edit modal clears the per-script cache and reloads from disk.

## Adding a new cue

1. Add the tag name string to `TAGS` array.
2. Add a multiplier to `CUE_MULT` (use `1.0` if no timing effect).
3. Add a `.c-newtag` CSS rule for the visual styling (color, weight, decoration).
4. If it has a karaoke-active animation, add `#card .c-newtag .kw.active { … }` rule. Reference `.c-hit` / `.c-stretch` for patterns.
5. Update the legend HTML inside `#legend`.
6. Update the cue table in this file.

## Common mistakes

- **Forgetting `{/}`** — unclosed tags break everything after them in that line.
- **Using `{emp}` on a whole sentence** — defeats the point; use it on 1–3 key words only.
- **Long source lines** — wrap awkwardly in karaoke. Split into 1–2 thought units per line.
- **Stacking all cues** — pick at most two. The multiplier compounds.
- **Writing English at his fluency level when he wants practice** — too easy = no growth. Too hard = stutters. Calibrate to challenging-but-readable.

## What "good" looks like

A finished script:
- 8–20 cards (lines), each readable in a single breath
- 60–90 second total at 100 wpm
- 3–4 `{hit}` placements at peak moments
- 1–2 `{rise}` building toward a payoff
- Real numbers / specifics, not abstractions
- Closes on a single thought, not a list

If you're producing a script and you have 6 cues stacked on one word, or three `{hit}` in a row, stop and re-read it as a human would.

---

Maintained at: `C:\xampp\htdocs\!PROJECTBANK\teleprompter\AGENTS.md`. README.md is the user-facing version. Update this doc when adding/removing cues, changing timing multipliers, or adding new modes.
