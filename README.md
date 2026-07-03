# html4presentation

**html4presentation** is an agent skill that generates single-file HTML presentations editable like PowerPoint — in the browser, offline, with no build step, server, or dependencies. Every deck it produces embeds a drop-in editor, so it ships ready to edit.

## How it works

A deck is one self-contained `.html` file: inline CSS, your slides, and a drop-in editor `<script>`. Open it by double-clicking — present with the arrow keys, or press **E** to edit. The editor injects its own interface and strips all of it on save, so the written file stays clean and re-opens editable.

## Features

- **Edit in place** — click any text to type; drag an image or video onto a media slot to replace it, or paste a screenshot with `Ctrl`/`Cmd`+`V` to add it to the slide (embedded as a data URL).
- **Restructure** — reorder, add, duplicate, and delete slides; cycle layouts; drag and resize elements with alignment guides.
- **Author tools** — undo/redo, text formatting, per-slide backgrounds, and a thumbnail sidebar.
- **Present** — press **F** for fullscreen; navigate with `←` `→` `Space`.
- **Save** — `Ctrl`/`Cmd`+`S`. Chromium writes straight back to the file; other browsers download an updated copy.
- **Cross-deck copy** — with no element selected, `Ctrl`/`Cmd`+`C` copies the current slide and `Ctrl`/`Cmd`+`V` pastes it into another html4pre deck.
- **Export** — choose a resolution preset or custom pixel size, then download PDF or image-based PPTX. Embedded video is exported only as part of the slide image, not as playable video.
- **No dependencies** — vanilla JS in one classic `<script>`; runs from `file://`.

## Quick start

Open the reference deck and try it:

```
demo/index.html
```

Press **E** to edit, **F** to present, `Ctrl`/`Cmd`+`S` to save, or open **Export** for PDF/PPTX. A second example lives at `demo/genshin-impact.html`.

## Generate a deck with an agent

The editor is delivered as a portable skill — one source of truth, two entry points:

- **Claude Code** — install `skills/html4pre/`, then ask for a deck (or run `/html4pre`).
- **Codex and other agents** — `AGENTS.md` points them at `skills/html4pre/SKILL.md`.

Ask for something like *“make me a 6-slide deck about X.”* The agent emits one `.html` with the editor embedded.

## Authoring contract

```html
<div class="deck"
     data-layouts="default title statement image-right center"
     data-slide-width="1920"
     data-slide-height="1080">
  <section class="slide is-active">
    <div class="stage" data-layout="title">
      <h1 data-editable>Headline</h1>
      <p data-editable>Subtitle</p>
    </div>
  </section>
  <!-- editor block from skills/html4pre/assets/deck-editor.inline.html, pasted verbatim -->
</div>
```

- `.deck` › `.slide` › `.stage` — the first slide carries `is-active`; the deck declares a logical slide size and the editor scales each `.stage` to fit the viewport.
- `data-editable` marks editable text; `data-slot` marks swappable image/video media slots.
- `data-layout` sets a stage's layout; `data-layouts` lists what **Layout** cycles through.
- `data-slide-width` / `data-slide-height` set the deck's logical slide size; new decks should default to `1920×1080`. PDF/PPTX export can render at any requested pixel size, and PPTX output is full-slide images rather than editable PowerPoint text boxes or playable video.

Full reference: [`skills/html4pre/references/convention.md`](skills/html4pre/references/convention.md).

## Repository layout

| Path | Purpose |
| --- | --- |
| `skills/html4pre/SKILL.md` | The skill an agent follows to generate a deck |
| `skills/html4pre/references/convention.md` | Attribute reference and copy-paste layout CSS |
| `skills/html4pre/assets/deck-editor.inline.html` | The drop-in editor — single source of truth |
| `demo/` | Working reference decks |
| `AGENTS.md` | Entry point for Codex and other agents |
