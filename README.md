# Teleprompter

Standalone speaking-practice teleprompter with delivery cue annotations.

## Open
- Direct: `file:///C:/xampp/htdocs/!PROJECTBANK/teleprompter/index.html`
- Via XAMPP: `http://localhost/!PROJECTBANK/teleprompter/`

## Controls
| Key | Action |
|---|---|
| Space | Play / pause |
| ↑ / ↓ | Speed +/- 5 wpm |
| + / − | Font size |
| R | Reset to top |
| F | Fullscreen |
| M | Mirror (for teleprompter rigs) |
| L | Toggle legend |
| H | Hide UI |
| E | Edit script |
| PgUp / PgDn | Skip half-screen |

Edit Script saves to localStorage — survives reload until you Cancel-edit-with-empty or clear browser data.

## Cue Syntax

Inline tags wrap text with `{tag}…{/}`. Tags can nest: `{up}{loud}HUGE POINT{/}{/}`.

| Tag | Technique | Visual |
|---|---|---|
| `{loud}` | Volume up — command the room | bold, larger, orange |
| `{soft}` | Volume down | smaller, gray |
| `{whisper}` | Near-whisper, pulls audience in | italic, dotted underline |
| `{slow}` | Slow tempo, weight a complex point | wide letter-spacing, blue underline |
| `{fast}` | Fast tempo, build excitement | tight italic, orange |
| `{up}` | Pitch rises | ↑ prefix, golden |
| `{down}` | Pitch falls | ↓ prefix, dim |
| `{emp}` | Emphasis on a key word/number | yellow highlight |
| `{urg}` | Urgent tonality, stakes high | red, bold |
| `{warm}` | Empathetic tonality | warm pink-orange |

Block / inline markers:

| Marker | Effect |
|---|---|
| `{pause:2}` on its own line | full block "··· 2s pause ···" |
| `{pause:1}` inside a line | inline pause dots |
| `{breath}` on its own line | block breath mark |
| `{breath}` inline | small ↻ marker |

## Rate of Speech defaults
- Slider: 40–220 wpm. Default 100 (conversational).
- Conversational English: ~130–150 wpm. Slow / weighty: 90–110. Fast / hype: 160+.
- Calibration is approximate (assumes ~7 words per visible line). If a section feels too fast/slow, just nudge the slider — your perceived speed is what matters.

## Scripts

Scripts now live as plain `.txt` files in the `scripts/` folder and are picked from the **Script** dropdown in the toolbar. The embedded default in `index.html` is the fallback when nothing is selected (or when opened via `file://` so `fetch` is unavailable).

### Folder layout
```
teleprompter/
  index.html
  scripts/
    index.json                    ← manifest, lists what shows in the dropdown
    lalafun-cost-per-piece.txt
    <your-new-script>.txt
```

### Rules for adding a new script (read this before creating files)

1. **One script = one `.txt` file** in `scripts/`. UTF-8, plain text, no front-matter, no HTML wrapper. The file's contents are the raw cue source — same syntax as the embedded default.
2. **Filename:** lowercase-kebab-case, descriptive, prefixed by topic if useful. Examples: `floural-launch-hook.txt`, `lalafun-staff-pep.txt`, `personal-brand-intro-30s.txt`. No spaces, no Indonesian unless it's a proper noun.
3. **Register it in `scripts/index.json`** — the dropdown only shows entries listed there. Append an object:
   ```json
   { "file": "your-script.txt", "title": "Short Human Title" }
   ```
   `title` is what shows in the dropdown (~30 chars max).
4. **Cue grammar:** see `AGENTS.md` for the full cue table, multipliers, and script-writing principles. Don't invent new cues without also wiring them into `index.html` (`TAGS`, `CUE_MULT`, CSS).
5. **Don't edit other people's scripts in place** unless asked — copy to a new filename and register it. Scripts are draft artifacts; preserve history by adding rather than mutating.
6. **Don't put scripts anywhere else** (no nested folders, no `.md`, no JSON-wrapped content). The loader only fetches `scripts/<file>` flat.

### How edits behave (so you don't lose work)
- Picking a script in the dropdown loads it from disk (or from the in-browser cache if you've edited it).
- **Edit Script** saves your changes to `localStorage` keyed per file (`teleprompter.script.<file>`). Your edits survive reload but never touch the disk file.
- **↻ Reset to file** in the edit modal discards the cached edits and reloads from `scripts/<file>` on disk. Use this after updating the file on disk.
- Selecting `(default — embedded)` uses the script baked into `index.html` and the legacy single-key cache.

### Quick "Edit Script" workflow (for one-off practice text)
Click **Edit Script**, paste/type, **Reload**. It only lives in your browser. If you want to keep it across machines or share it, save it as a file in `scripts/` and register it in `index.json`.

## What's the included script
A 2–3 minute monologue on LalaFun unit economics — your real domain expertise — densely cued so you can drill the techniques while practicing English fluency. Once you're comfortable with the techniques, write your own scripts on whatever you want to film.
