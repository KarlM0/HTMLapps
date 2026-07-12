# Design Guidelines

This document defines the shared visual language for all apps in this family.
It is divided into two parts with different levels of obligation:

- **Part 1 — Universal foundations** apply to every app without exception:
  colour tokens, typography, base body styles, and iconography rules. These
  are non-negotiable and must be implemented as specified regardless of what
  the app does or how its layout is structured.

- **Part 2 — Reference components** are established patterns to draw from
  *when the component is needed*. They are not a required layout to impose on
  every app. A settings-heavy form app uses most of them; a card game or
  canvas tool may use only a few. When a component from this library is used,
  implement it exactly as specced. When a novel element is needed that this
  library does not cover, derive it from the Part 1 tokens so it still feels
  like part of the same family, state the derivation explicitly, and flag it
  as a candidate for addition to this document.

---

# Part 1 — Universal foundations

## Colour tokens

Define these as CSS custom properties on `:root`. Use the variable names
exactly as given. Never hard-code a colour value that has a token.

```css
:root {
  --bg:           #141518;   /* page background                        */
  --surface:      #1c1d22;   /* card / panel background                */
  --surface-2:    #24252c;   /* inset elements: inputs, chips          */
  --border:       #2e2f38;   /* standard border                        */
  --border-soft:  #26272f;   /* subtle separator                       */
  --text:         #e9e9ec;   /* primary text                           */
  --text-dim:     #9a9aa6;   /* secondary / label text                 */
  --text-faint:   #62636e;   /* tertiary: placeholders, metadata       */
  --accent:       #7cdcc4;   /* teal — interactive highlight, CTA      */
  --accent-deep:  #152823;   /* dark teal — selected-state background  */
  --ok:           #6fd089;   /* success / correct                      */
  --bad:          #e8736f;   /* error / incorrect                      */
  --track:        #34353f;   /* progress-bar / inactive track          */
  --radius:       14px;      /* larger corner radius (cards, panels)   */
  --radius-sm:    9px;       /* smaller corner radius (inputs, chips)  */
}
```

Warning / advisory states that do not map cleanly to `--bad` use a warm-amber
triplet: background `#2a2118`, border `#5a4427`, text `#e7c690`. These are not
tokens — use the hex values directly, as they are situational.

> **Changing the accent:** `--accent` (`#7cdcc4`) is a specific teal chosen
> for legibility against these dark backgrounds. If a future app warrants a
> different accent for thematic reasons, that is a legitimate decision — but it
> must be made deliberately, and all derived tokens (`--accent-deep`, focus
> rings, active states) must be updated together to match.

---

## Typography

Three typefaces, each with a fixed role. Load all three from Google Fonts and
use them only as described. Only load the weights listed.

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,500;9..144,600&family=Manrope:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
```

| Role | Family | Weights |
|---|---|---|
| Display / headings | Fraunces (serif, optical-size aware) | 500, 600 |
| Body / UI labels | Manrope (sans-serif) | 400, 500, 600, 700 |
| Numbers / code / monospaced data | JetBrains Mono | 400, 500, 700 |

### Type scale

These sizes apply wherever the corresponding element appears, regardless of
the app's overall layout.

- **`h1`** — Fraunces 600, 30px, `letter-spacing: -0.01em`, `line-height: 1.1`.
- **Large numerals** (scores, results, prominent data) — Fraunces 600, 34px.
- **Body copy and UI text** — Manrope, base 15px, `line-height: 1.5`.
- **Section / panel titles** — Manrope 700, 11px, `letter-spacing: 0.14em`,
  `text-transform: uppercase`, colour `--text-faint`.
- **Field labels** — Manrope 600, 12.5px, colour `--text-dim`.
- **Version string** — JetBrains Mono, 11px, `letter-spacing: 0.03em`,
  colour `--text-faint`.
- **All numeric inputs, displayed numbers, computed values, and code** —
  JetBrains Mono.

---

## Base body styles

Apply to every app:

```css
body {
  background: var(--bg);
  color: var(--text);
  font-family: 'Manrope', -apple-system, BlinkMacSystemFont, sans-serif;
  font-size: 15px;
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
```

Padding and layout structure beyond this are app-specific (see Part 2 for the
reference layout used in settings-style apps).

---

## Iconography

Use standard Unicode emoji and inline SVG only — no icon font, no icon
library.

- **UI chrome** (chevrons, close buttons, indicators): inline SVG.
- **Feedback and decorative elements**: Unicode emoji.

Inline SVG icons use `currentColor` for their stroke or fill so they inherit
colour from their context. The standard chevron:

```html
<svg viewBox="0 0 16 16" width="15" height="15">
  <path d="M4 6 L8 10 L12 6"
        stroke="currentColor" stroke-width="2"
        fill="none" stroke-linecap="round" stroke-linejoin="round"/>
</svg>
```

---

## Animations

The standard entrance animation for items appearing in a list or grid:

```css
@keyframes rise {
  from { opacity: 0; transform: translateY(7px); }
  to   { opacity: 1; transform: none; }
}
```

Duration `0.35s ease`. Apply with staggered `animation-delay`
(e.g. `index × 32ms`) so items appear sequentially. Use this or a close
derivative for any entrance animation; do not introduce unrelated motion
styles.

---

# Part 2 — Reference components

These components are used when the app calls for them. Each is specified
completely so that any app needing it implements it consistently.

---

## Page layout (settings-style apps)

For apps built around a settings panel plus a content area — the most common
structure in this family.

- Maximum content width: **760px**, centred with `margin: 0 auto`.
- Body padding: `34px 20px 80px`.
- Single responsive breakpoint at **560px**: collapse two-column grids to one
  column, reduce `h1` to 25px, reflow any side-by-side panels to vertical
  stacking.

Apps with a substantially different structure (games, canvases, dashboards)
should not impose this layout but still use the token set and typography.

---

## Page header

A flex row (`align-items: flex-start`, `justify-content: space-between`,
`gap: 20px`, `margin-bottom: 30px`):

- **Left:** `h1` (app name), subtitle below in Manrope 13.5px `--text-dim`,
  version string below that.
- **Right:** global controls (e.g. language switcher), `flex-shrink: 0`.

---

## Cards

The primary container unit for distinct sections of content.

```
background:    var(--surface)
border:        1px solid var(--border-soft)
border-radius: var(--radius)
padding:       22px
margin-bottom: 18px
```

### Collapsible cards

A collapsible card has a header row containing the section title on the left
and a chevron on the right (`cursor: pointer`, `user-select: none`).

- Body is hidden (`display: none`) when a `.collapsed` class is present on
  the card element.
- Chevron rotates `−90deg` when collapsed, via `transition: transform 0.2s ease`.
- Header has `margin-bottom: 20px` only when expanded.

---

## Buttons

All buttons share: `border-radius: var(--radius-sm)`, Manrope 700, 14.5px,
`padding: 14px`, `letter-spacing: 0.01em`, `transition: 0.16s`, `border: none`,
`cursor: pointer`. Full-width by default.

| Variant | Background | Text | Hover |
|---|---|---|---|
| Primary | `var(--accent)` | `#0e1714` | `filter: brightness(1.08)` |
| Ghost | `var(--surface-2)`, border `1px solid var(--border)` | `var(--text)` | border + text → `var(--accent)` |

Disabled state (both): `opacity: 0.35`, `cursor: not-allowed`, no hover effect.

---

## Form controls

### Inputs and selects

```
background:    var(--surface-2)
border:        1px solid var(--border)
color:         var(--text)
font-size:     14px
padding:       9px 11px
border-radius: var(--radius-sm)
transition:    0.16s
```

- Number inputs: JetBrains Mono. Selects: Manrope, `cursor: pointer`.
- Focus: `outline: none`, `border-color: var(--accent)`.
- Disabled: `opacity: 0.4`, `cursor: not-allowed`.

### Toggle switches

Custom-styled checkboxes. Track: 42×24px pill, background `var(--surface-2)`,
border `1px solid var(--border)`. Knob: 16×16px circle, background
`var(--text-faint)`, 3px from the left edge. Checked: track background
`var(--accent-deep)`, track border `var(--accent)`, knob shifts 18px right and
becomes `var(--accent)`. All transitions `0.18s`.

Switch rows: label/description on the left, toggle on the right, separated by
`border-top: 1px solid var(--border-soft)`, padding `11px 0`. Label: Manrope
600, 13.5px. Description: Manrope 11.5px, `var(--text-faint)`.

---

## Selection chips

Toggleable option tiles, arranged in a CSS grid. Each chip:

```
background:    var(--surface-2)
border:        1.5px solid var(--border)
border-radius: var(--radius-sm)
padding:       12px 6px
display:       flex, column, centred
transition:    0.16s
cursor:        pointer
user-select:   none
```

- Symbol: JetBrains Mono, 24px, `var(--text-dim)`.
- Label: Manrope, 11.5px, `var(--text-faint)`.
- Active: border → `var(--accent)`, background → `var(--accent-deep)`,
  symbol → `var(--accent)`, label → `var(--text)`.

---

## Progress bars

Pill track: `height: 9px`, `border-radius: 999px`, background `var(--track)`,
`overflow: hidden`. Filled segments: `transition: width 0.35s ease`.

- `var(--ok)` — correct / complete.
- `var(--bad)` — incorrect.
- The unfilled track needs no extra segment.

---

## Pill button group

Compact multi-option selector (e.g. language switcher). Outer container:
background `var(--surface)`, border `1px solid var(--border)`,
`border-radius: 999px`, `padding: 3px`. Inner buttons: transparent, colour
`var(--text-dim)`, Manrope 600, 12px, `letter-spacing: 0.03em`,
`padding: 5px 11px`, `border-radius: 999px`, `transition: 0.18s`. Active:
background `var(--accent)`, text `#0e1714`.

---

## File / folder picker

A dashed-outline tile that stands in for a native file or folder input. The
whole tile is the click target: wrap a visually hidden `<input type="file">`
in a `<label>` styled as below (`input[type=file] { display: none }`).

```
display:       block
border:        1.5px dashed var(--border)
background:    var(--surface-2)
border-radius: var(--radius-sm)
padding:       18px
text-align:    center
cursor:        pointer
transition:    0.16s
```

States:

- **Hover / drag-over** (`.drag`) — `border-color: var(--accent)`.
- **Loaded** (a file or folder has been chosen) — `border-style: solid`,
  `border-color: var(--accent)`.

Contents, stacked and centred:

- **Glyph** — a Unicode emoji, 22px, `display: block`, `margin-bottom: 6px`
  (e.g. 📁 for a folder, 🗂️ for a file, 🖼️ for an image).
- **Title** — Manrope 600, 13.5px.
- **Meta line** — JetBrains Mono, 11px, `var(--text-faint)`, `margin-top: 5px`;
  holds the chosen name, a file count, or a placeholder.

Two pickers side by side sit in a two-column grid (`gap: 12px`) that collapses
to a single column at the 560px breakpoint.

---

## Review grid

A compact table for reviewing a list of detected items before committing an
action — each row carrying one or more thumbnails and an include toggle. The
header row and the data rows share one grid template so columns line up.

```
.review-head, .review-row
  display:               grid
  grid-template-columns: 1fr 88px 88px 52px      /* name · A · B · include */
  gap:                   10px
  align-items:           center
  padding:               9px 8px
```

- **Header** — Manrope 700, 10px, `letter-spacing: 0.12em`, uppercase,
  `var(--text-faint)`, `border-bottom: 1px solid var(--border-soft)`.
- **Data row** — `border-bottom: 1px solid var(--border-soft)`; enters with the
  `rise` animation, staggered `index × 32ms`.
- **Incomplete / advisory row** (`.missing`) — uses the Part 1 warm-amber
  advisory triplet: background `#2a2118`, border `#5a4427`.
- **Error row** (`.error`) — a translucent `--bad` tint,
  `rgba(232, 115, 111, 0.08)`.
- **Single-thumbnail variant** (`.back-row`) —
  `grid-template-columns: 1fr 88px 52px`.

Name cell: JetBrains Mono, 12px, truncated with `text-overflow: ellipsis`.
Inline status icons use amber `#e7c690` (advisory) or `var(--bad)` (error).

Thumbnails: 60×60, `border-radius: var(--radius-sm)`, `object-fit: cover`,
background `var(--surface-2)`. A `.big` modifier is 80×80. An empty / placeholder
cell centres a glyph in `var(--text-faint)` at 18px.

A summary line above the grid (JetBrains Mono, 12px, `var(--text-dim)`) counts
outcomes, emphasising figures with `--ok`, amber `#e7c690`, and `--bad`.

Responsive (≤560px): columns tighten to `1fr 64px 64px 46px`
(single-thumbnail variant `1fr 64px 46px`).

---

## Selectable list

A vertical stack of selectable rows for choosing one item from several — for
example, picking one set among those found in a folder. The list chrome and
selection behaviour below are the reusable part; the *content* of each row is
app-specific.

```
.set-list (container)
  display:        flex
  flex-direction: column
  gap:            8px
```

Each row:

```
background:    var(--surface-2)
border:        1.5px solid var(--border)
border-radius: var(--radius-sm)
padding:       14px
transition:    border-color 0.16s, background 0.16s
cursor:        pointer
```

- **Hover** — `border-color: var(--accent)`.
- **Selected** — `border-color: var(--accent)`, `background: var(--accent-deep)`
  (the same active treatment as selection chips).
- Rows enter with the `rise` animation, staggered `index × 32ms`.

Row content is laid out by the app. As a reference instantiation, each row is a
flex pair (`gap: 16px`, `align-items: flex-start`): a text block on the left
(title in Fraunces 600 18px; supporting metadata in Manrope / JetBrains Mono per
the type scale) and a thumbnail on the right.

---

## Loading indicator

A quiet, centred status line shown while an async operation runs (e.g. reading a
folder). It reuses the section-title type treatment, centred with vertical
padding.

```
font-family:    'Manrope', 700
font-size:       11px
letter-spacing:  0.14em
text-transform:  uppercase
color:           var(--text-faint)
text-align:      center
padding:         18px 0
```
