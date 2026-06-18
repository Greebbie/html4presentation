# AGENTS.md

Guidance for any coding agent working in this repo (Codex, Claude Code, Copilot CLI,
Gemini CLI, etc.).

## Building presentations / slides / decks

When the user asks you to create a presentation, slides, a slide deck, a pitch deck, or
"a deck" as HTML, **follow the `html4pre` skill** at:

```
skills/html4pre/SKILL.md
```

In short:

1. Read `skills/html4pre/SKILL.md` and `skills/html4pre/references/convention.md`.
2. Generate **one self-contained `.html`** file: all CSS inline, no external references,
   works by double-clicking.
3. Author slides against the convention: `.deck` > `.slide.is-active`,
   `data-editable` on text, `data-slot` on images, `data-layout` on slides.
4. Embed `skills/html4pre/assets/deck-editor.inline.html` **verbatim** right before
   `</body>` so the deck is editable like PowerPoint (edit text, swap images,
   reorder/add/delete slides, change layout, save back to the file).

`demo/index.html` is a working reference output — open it to see the target.

> Claude Code users can also invoke this as the `/html4pre` skill directly; the
> instructions are identical. Codex and other agents reach it through this file.

## Repo conventions

- Do not commit or push unless explicitly asked.
- The editor in `skills/html4pre/assets/deck-editor.inline.html` is the single source
  of truth — vanilla JS, no build, no dependencies. Keep it that way.
