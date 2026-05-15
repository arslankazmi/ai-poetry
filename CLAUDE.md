# ai-poetry — Claude Code notes

## What this repo is

An anthology of poems written by Claude models, each in the classical form that shares the model's name. A haiku by Haiku. A sonnet by Sonnet. An opus by Opus.

## Adding a new poem

### 1. File location
Place the poem in the correct subdirectory by form:
- `haiku/your-title-slug.md`
- `sonnet/your-title-slug.md`
- `opus/your-title-slug.md`

### 2. Required frontmatter

```yaml
---
layout: default
title: "Your Poem Title"
form: haiku              # haiku | shakespearean-sonnet | opus
model: claude-haiku-4-5  # exact model ID used to generate the poem
date: YYYY-MM-DD
preview: "First line,\nSecond line,\nThird line."
---
```

The `preview:` field feeds the card on the anthology index — use `\n` for line breaks. Keep it to the opening stanza.

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

### 5. Commit checklist

- [ ] Correct `form:` frontmatter matches the subdirectory
- [ ] `data-dropcap="<LETTER>"` set on `.poem-body`
- [ ] `class="poem-opening"` on the correct paragraph
- [ ] `GoudyInitialen-<LETTER>.ttf` present in `assets/dropcaps/`
- [ ] `@font-face` + CSS rule for the letter exist in `_layouts/default.html`
- [ ] `preview:` field present for the anthology card

## Tech stack

- Jekyll + GitHub Pages (no build step beyond `git push`)
- Custom `_layouts/default.html` — no theme dependency
- `_layouts/anthology.html` extends default; loops `site.pages` by `form:` automatically
- Goudy Initialen (gwern.net) for drop caps, EB Garamond for body, Cinzel for headings
- Three-column responsive grid (1280px max, collapses to single column on mobile)
