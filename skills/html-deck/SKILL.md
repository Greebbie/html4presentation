---
name: html-deck
description: Use when the user wants to create a presentation, slides, slide deck, or "a deck" as HTML. Generates a single self-contained .html file that is editable like PowerPoint — the recipient can drag images to replace them, click text to edit, reorder/add/delete slides, change layout, and save back to the file — by embedding a drop-in editor. Works offline by double-clicking the file. Use for any "make me slides / a pitch deck / a talk" request where the output should be HTML.
---

# html-deck — generate decks that are born editable

Produce **one self-contained `.html` file** that presents beautifully *and* can be
edited like PowerPoint with no tools, no server, and no AI — just by opening the file
in a browser. You do this by (1) authoring slides against a small convention and (2)
embedding the deck editor block verbatim.

## The output contract

Every deck you generate MUST be a single `.html` file with this shape:

```html
<!DOCTYPE html>
<html lang="...">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>...</title>
  <style> /* ALL the deck's visual CSS goes here, inline */ </style>
</head>
<body>
  <div class="deck" data-layouts="default title statement image-right center">
    <section class="slide is-active">
      <div class="stage" data-layout="title">
        <h1 data-editable>...</h1>
        <p data-editable>...</p>
      </div>
    </section>
    <!-- more <section class="slide"><div class="stage" data-layout="..."> ... -->
  </div>

  <!-- Editor: paste assets/deck-editor.inline.html here, verbatim, unchanged -->
</body>
</html>
```

Hard rules — do not deviate:

1. **One file.** Inline all CSS in a `<style>`. No external stylesheets, no module
   scripts, no CDN links, no `./src/` references. It must work by double-clicking.
2. **Embed the editor verbatim.** Read `assets/deck-editor.inline.html` (relative to
   this skill) and paste its entire contents — the comment and the `<script>` — right
   before `</body>`. Do not edit, minify, or "improve" it.
3. **Follow the markup contract** (`.deck > .slide > .stage`) so the editor can find things:
   - Slides are `<section class="slide">` inside `<div class="deck">`.
   - Each slide wraps its content in a single `<div class="stage">` (the fixed 1280×720
     canvas the editor scales to fit).
   - The first slide also has `is-active`.
   - Every editable text element gets `data-editable`.
   - Every swappable `<img>` gets `data-slot`.
   - `data-layout="<name>"` goes on the **`.stage`** (not the slide); declare the names on
     the deck via `data-layouts="..."`.
   - Author at fixed px sizes (not `vw`/`clamp()`); the editor scales the whole stage.
4. **Images:** prefer an inline SVG `data:` URI placeholder so swap works offline and
   the file stays portable. If you use a real image, keep it as a `data:` URI too.
5. **Slide visibility is owned by the editor** (`.slide{display:none}` /
   `.slide.is-active{...}` is injected). Your CSS styles *look*, not show/hide — but
   you may override visibility with higher specificity if you need transitions.

See `references/convention.md` for the full attribute reference and a copy-paste CSS
recipe for the standard layouts.

## Process

1. Clarify only what you must (topic, audience, rough slide count, vibe). Don't
   over-ask — a deck is easy to edit afterward, which is the whole point.
2. Design the visual system in the inline `<style>`: typography scale, colors, spacing,
   and the layout rules keyed off `data-layout`. Aim for distinctive, modern, high-
   contrast design — not a generic template.
3. Write the slides using the convention. Mark every headline, paragraph, list, and
   image with `data-editable` / `data-slot`.
4. Embed the editor block verbatim before `</body>`.
5. Tell the user how to use it: open the file, press **E** to edit, click text to type,
   drag an image onto a picture to replace it, use the bottom bar to reorder/add/delete
   slides and cycle layout, then **Save** (Ctrl/Cmd+S).

The embedded editor now supports: drag/resize elements with alignment guides, undo/redo,
text formatting (size/bold/italic/underline/color/align), insert text box / shape / image,
per-slide background, a thumbnail sidebar with drag-to-reorder, and layout templates that
add new pre-built slides. After editing, elements a user dragged or resized carry
`data-free` plus inline `left/top/width/height` geometry — that's expected and still valid
markup (the editor and a re-opened saved file both honor it).

## Self-check before finishing

- [ ] Single file, all CSS inline, no external references.
- [ ] Editor block pasted verbatim before `</body>`.
- [ ] `.deck` > `.slide` > `.stage` structure; first slide has `is-active`.
- [ ] Stage model: each slide wraps content in `.stage` (1280×720); fixed px type, not vw/clamp.
- [ ] `data-editable` on all text; `data-slot` on all images.
- [ ] `data-layout` on each `.stage`; `data-layouts` on the deck.
- [ ] Opening the file shows slide 1; arrows navigate; **E** enters edit mode.
