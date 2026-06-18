# Deck convention reference

The editor (`assets/deck-editor.inline.html`) keys off these attributes. Author decks
to match and everything "just works."

## Structure

Each slide wraps its content in a fixed-size **`.stage`**. The deck declares the logical
slide size with `data-slide-width` and `data-slide-height`; new decks should default to
`1920×1080`. The editor scales every `.stage` to fit the viewport (`transform: scale()`),
so you author at that fixed logical canvas and it renders crisp at any window size.

Decks without slide-size metadata still open with the legacy `1280×720` fallback.

```html
<div class="deck"
     data-layouts="default title statement image-right center"
     data-slide-width="1920"
     data-slide-height="1080">
  <section class="slide is-active">
    <div class="stage" data-layout="title"> ... </div>
  </section>
  <section class="slide">
    <div class="stage" data-layout="default"> ... </div>
  </section>
</div>
```

| Selector / attribute | Meaning |
|----------------------|---------|
| `.deck` | Wrapper for all slides. Optional but recommended; the editor scopes to it. Carries `data-layouts` and slide-size metadata. |
| `.slide` | One slide. The editor shows one at a time. Holds a single `.stage`. |
| `.stage` | Fixed logical canvas inside the slide; holds all slide content. Carries `data-layout` and (optionally) an inline `background`. The editor sizes it from deck metadata and scales it to fit. |
| `.is-active` | The currently visible slide. Put it on the **first** `.slide`; the editor manages it afterward. |
| `data-editable` | Marks a text element as click-to-edit. |
| `data-slot` | On an `<img>`: marks it swappable (click or drag-drop a file). |
| `data-layout="x"` | Per-stage layout name (lives on `.stage`, not `.slide`); your CSS styles `.stage[data-layout="x"]`. |
| `data-layouts="a b c"` | On `.deck`: the layout names the **Layout** button cycles through. |
| `data-slide-width`, `data-slide-height` | On `.deck`: the logical slide size. Use `1920` and `1080` for default 16:9 decks. |
| `data-free` | On any stage child: marks it as freely positioned (see below). Editor-managed; you normally don't hand-author it. |

If a deck has **no** `data-editable` / `data-slot`, the editor auto-detects text
elements and `<img>`s. The explicit attributes are more reliable — always add them when
generating.

## Free positioning (editor-managed)

Any element the user drags becomes `position:absolute` with `data-free` and inline
`left/top/width/height` in the deck's logical slide px (`0–1920 × 0–1080` for a default
deck). You normally generate content in flow inside `.stage`; the editor adds `data-free`
on demand when someone drags or resizes an element. Don't hand-author `data-free` unless
you want an element pre-placed at fixed coordinates. Example of an editor-produced free
element in a default `1920×1080` deck:

```html
<div data-editable data-free
     style="position:absolute;left:180px;top:780px;width:630px;height:90px;margin:0;font-size:42px">
  A free-floating caption.
</div>
```

## What the editor injects (don't redefine unless intentional)

- Slide visibility: `.deck .slide{display:none}` and `.deck .slide.is-active{display:flex}`.
- **Stage geometry + scaling**: `.deck .slide .stage` is sized from deck metadata and scaled
  via `transform: scale(var(--ed-scale))` (the editor computes `--ed-scale` to fit the
  viewport). It also centers the stage in the slide. Don't override the stage's
  `width`/`height`/`transform`.
- Its own toolbar, sidebar, format bar, and edit-mode outlines (all under `.ed-*` /
  `[data-ed-*]` / `[data-editor-chrome]`, stripped on save).

Your inline `<style>` owns everything else: fonts, colors, type sizes, backgrounds, and the
per-layout rules below — all keyed off `.stage`.

## Copy-paste CSS recipe for the standard layouts

Author at the deck's fixed logical stage: use **fixed px** sizes (not `clamp()` / `vw` /
`vh`), because the editor scales the whole stage uniformly. This is the `1920×1080`
recipe `demo/index.html` ships.

```css
:root { --bg:#0f172a; --fg:#e2e8f0; --muted:#94a3b8; --accent:#38bdf8; }
* { box-sizing:border-box; }
html,body { margin:0; height:100%; }
body { background:#0b0f1a; color:var(--fg);
  font-family:"Inter",system-ui,-apple-system,"Segoe UI",Roboto,sans-serif;
  -webkit-font-smoothing:antialiased; }
.deck { height:100vh; }

/* The logical slide canvas. The editor sizes/scales/centers it — you style its inside. */
.stage { background:var(--bg); display:flex; flex-direction:column;
  justify-content:center; gap:36px; padding:108px 144px; }
.stage > * { margin:0; }
.stage h1 { font-size:108px; line-height:1.05; letter-spacing:0; margin:0; }
.stage h2 { font-size:69px; line-height:1.1; margin:0 0 12px; }
.stage p  { font-size:36px; line-height:1.5; color:var(--muted); margin:0; }
.stage ul { font-size:36px; line-height:1.7; padding-left:1.2em; margin:0; }
.stage .accent { color:var(--accent); }

/* layouts (keyed off data-layout on .stage) */
.stage[data-layout="title"], .stage[data-layout="statement"],
.stage[data-layout="center"] { text-align:center; align-items:center; }
.stage[data-layout="statement"] h1 { font-size:156px; }
.stage[data-layout="title"] .kicker { text-transform:uppercase; letter-spacing:3px;
  font-size:23px; color:var(--accent); }

.stage[data-layout="image-right"] > .row { display:grid; grid-template-columns:1fr 1fr;
  gap:96px; align-items:center; width:100%; }
.stage[data-layout="image-right"] figure { margin:0; }
.stage[data-layout="image-right"] img { width:100%; height:auto; border-radius:24px;
  display:block; box-shadow:0 20px 50px rgba(0,0,0,.45); }
```

## Export

The editor can export PDF and PPTX from the **Export** dialog. Slide Size controls the
deck's logical canvas; Export Resolution controls output pixels. Both exports render each
slide as a bitmap at the chosen export width/height. If that export ratio differs from
the deck's logical slide ratio, the slide is stretched to fill. PPTX output is one
full-slide image per page, not editable PowerPoint text boxes.

## Offline image placeholder

Use an inline SVG `data:` URI so image-swap works with no network and the file stays
self-contained:

```html
<img data-slot src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='800' height='600'%3E%3Crect width='100%25' height='100%25' fill='%23e2e8f0'/%3E%3Ctext x='50%25' y='50%25' font-family='sans-serif' font-size='30' fill='%2364748b' text-anchor='middle' dominant-baseline='middle'%3EDrop an image here%3C/text%3E%3C/svg%3E" alt="">
```

## Notes on layout cycling

The **Layout** button only swaps the `data-layout` value (on the active `.stage`) through
`data-layouts`. It does not restructure a slide's content. So:

- Alignment/emphasis layouts (`default`, `title`, `statement`, `center`) work on any
  slide.
- Content-shaped layouts (`image-right`, two-column) need matching markup (a `.row`
  with a `<figure>`). Generate those slides already in that shape; cycling *into* them
  on a slide that lacks the markup will just re-align, not invent an image.
