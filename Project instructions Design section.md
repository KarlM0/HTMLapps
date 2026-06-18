## Design

All apps in this family share a single visual language defined in **`DESIGN.md`**,
which is stored in the Claude Project files and available in every conversation
in this project.

**Before writing any HTML, CSS, or layout code, read `DESIGN.md` in full.**

`DESIGN.md` is divided into two parts with different levels of obligation:

**Part 1 — Universal foundations** apply to every app without exception.
Implement these exactly as specified regardless of what the app does or how
its layout is structured:

- CSS custom properties (colour tokens) and their exact hex values — never
  hard-code a colour that has a token
- The three-typeface system (Fraunces / Manrope / JetBrains Mono) and the
  rules governing which typeface is used where
- Base body styles
- Iconography rules (inline SVG and Unicode emoji only; no icon libraries)
- The `rise` entrance animation and its staggered-delay convention

**Part 2 — Reference components** are established patterns to draw from
*when the component is needed*. They are not a required layout to impose on
every app. A settings-heavy form app will use most of them; a game or
canvas tool may use only a few. When a component from this library is used,
implement it exactly as specified.

### Handling novel elements

When an app requires a UI element that `DESIGN.md` does not cover (for
example, a flip card, a game board, or a canvas overlay), derive it from the
Part 1 tokens — use the colour variables, typefaces, radii, and motion
conventions — so it feels like part of the same family. State the derivation
explicitly in the code and flag the new pattern as a candidate for addition
to `DESIGN.md`.

### What not to do

- Do not introduce colours, typefaces, or spacing values outside the token
  set without an explicit decision.
- Do not change a token value without updating all derived tokens and states
  that depend on it.
- Do not skip reading `DESIGN.md` on the assumption that the current app's
  layout differs from a previous one — Part 1 applies universally.
