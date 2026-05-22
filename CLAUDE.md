# ai-poetry — Claude Code notes

## What this repo is

An anthology of poems written by Claude models, each in the classical form that shares the model's name. A haiku by Haiku. A sonnet by Sonnet. An opus by Opus.

## Adding a new poem

### 1. File location
Place the poem in the correct subdirectory by form:
- `haiku/your-title-slug.md`
- `sonnet/your-title-slug.md`
- `opus/your-title-slug.md`

### 2. Required frontmatter — fill every field

```yaml
---
layout: default
title: "Your Poem Title"
form: haiku              # haiku | shakespearean-sonnet | opus
model: claude-haiku-4-5  # exact model ID used to generate the poem
date: YYYY-MM-DD         # full ISO date — shown as "15 May 2026" on the site
preview: "First line,\nSecond line,\nThird line."
---
```

**`preview:` is mandatory.** It feeds the card excerpt on the anthology index. Use `\n` for line breaks. If it is missing, the poem card will appear with a title and date but no excerpt — do not leave it blank. Use the opening stanza (or the full poem for haiku).

**`date:` must be a full ISO date** (`YYYY-MM-DD`), not just a year. The anthology displays it as `%-d %B %Y` (e.g. `15 May 2026`). A missing or malformed date will break sorting (newest-first) and show nothing in the byline.

### 3. Dropcap — mandatory for every poem

Every poem **must** have a Goudy Initialen drop cap on its opening line. This is a hard requirement.

**Step A:** In the poem body, mark the opening paragraph with `class="poem-opening"` and set `data-dropcap="<LETTER>"` on the `.poem-body` div, where `<LETTER>` is the uppercase first letter of the first word:

```html
<div class="poem-body" data-dropcap="S">
<p class="poem-opening">Small and swift,<br>
Threading through the world's vast task —<br>
Less, yet holds it all.</p>
</div>
```

**Step B:** Check whether the Goudy Initialen TTF for that letter is already in `assets/dropcaps/`. List what's there:

```bash
ls assets/dropcaps/
```

**Step C:** If `GoudyInitialen-<LETTER>.ttf` is missing, download it:

```python
import urllib.request
letter = "X"  # replace with your letter
url = f"https://raw.githubusercontent.com/gwern/gwern.net/master/font/dropcap/goudy/GoudyInitialen-{letter}.ttf"
req = urllib.request.Request(url, headers={"User-Agent": "Mozilla/5.0"})
with urllib.request.urlopen(req) as r, open(f"assets/dropcaps/GoudyInitialen-{letter}.ttf", "wb") as f:
    f.write(r.read())
```

**Step D:** If the letter is new (not already in `_layouts/default.html`), add two blocks to `default.html`:

1. A `@font-face` declaration (in the dropcap font section):
```css
@font-face {
  font-family: 'dropcap-X';
  src: url('{{ "/assets/dropcaps/GoudyInitialen-X.ttf" | relative_url }}') format('truetype');
  font-display: block;
}
```

2. A CSS rule (in the letter-specific dropcap rules section):
```css
.poem-body[data-dropcap="X"] > p.poem-opening::first-letter {
  font-family: 'dropcap-X', 'Cinzel', serif;
}
```

### 4. For Opus poems with movement headers

If the poem has section markers (e.g. `I. Invocation`), mark them with `class="movement"` and ensure `poem-opening` is on the **first stanza paragraph**, not the movement label:

```html
<div class="poem-body" data-dropcap="T">

<p class="movement"><em>I. Invocation</em></p>

<p class="poem-opening">They named me for the work…</p>
```

### 5. Sources & inline links — when the poem draws on news

Many poems in this anthology are written in response to that day's news summary (`news-summary-YYYY-MM-DD.md` in the daily-update project). When a poem has clear news echoes, attribute them:

**(a) Inline links** — wrap 1–3 short concrete phrases inside the poem body in `<a href="https://url">phrase</a>` HTML. Choose phrases that are direct echoes of a specific headline: a noun phrase, a place, a number, a named thing. Do not link abstract or stylistic terms. Caps:

- Haiku: at most 1 inline link.
- Sonnet: at most 3 inline links.
- Opus: at most 5 inline links, ideally spread across movements.

Use HTML `<a>` tags rather than Markdown `[phrase](url)` — the poem body lives inside `<p>` blocks (HTML), and kramdown does not process Markdown links inside HTML block elements by default. Place the `<a>` inline; do not break the `<br>` cadence of the poem.

**(b) A Sources section** at the bottom of the file, between the poem body and the `← back to anthology` link, separated by `---` rules on both sides:

```markdown
</div>

---

<p class="poem-sources"><strong>Sources</strong> &nbsp;·&nbsp; <a href="https://example.com/story-1">Short Story Label</a> &nbsp;·&nbsp; <a href="https://example.com/story-2">Short Story Label 2</a></p>

---

← [back to anthology]({{ '/' | relative_url }})
```

List 2–5 sources. Each label is a short headline-style phrase, not a full URL. Use the article URL from the day's news-summary file (deep links preferred; fall back to root domains only if the summary itself only has the root). When a poem spans news from multiple days (e.g. dated 22 May but drawing on 21 May coverage), cite from both summaries.

**Poems that are purely about the poetic form itself** (e.g. a haiku reflecting on what a haiku is) — omit both inline links and the Sources section. The introductory form-poems in this anthology do not have sources.

### 6. Commit checklist

- [ ] Correct `form:` frontmatter matches the subdirectory
- [ ] `date:` is a full ISO date (`YYYY-MM-DD`)
- [ ] `preview:` is filled with the opening stanza (never blank)
- [ ] `data-dropcap="<LETTER>"` set on `.poem-body`
- [ ] `class="poem-opening"` on the correct paragraph
- [ ] `GoudyInitialen-<LETTER>.ttf` present in `assets/dropcaps/`
- [ ] `@font-face` + CSS rule for the letter exist in `_layouts/default.html`
- [ ] If the poem draws on news: inline links added (within caps) and `<p class="poem-sources">` block in place with 2–5 source links from the matching `news-summary-YYYY-MM-DD.md`

## Tech stack

- Jekyll + GitHub Pages (no build step beyond `git push`)
- Custom `_layouts/default.html` — no theme dependency
- `_layouts/anthology.html` extends default; loops `site.pages` by `form:` automatically
- Dates displayed as `%-d %B %Y` (e.g. `15 May 2026`) via `{{ poem.date | date: "%-d %B %Y" }}`
- Goudy Initialen (gwern.net) for drop caps, EB Garamond for body, Cinzel for headings
- Three-column responsive grid (1280px max, collapses to single column on mobile)
- Poem body centred as a block within the prose column (`width: fit-content; margin: auto`)
