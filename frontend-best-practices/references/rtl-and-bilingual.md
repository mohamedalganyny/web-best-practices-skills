# RTL and bilingual sites

Supports LAY-2, LAY-8 to LAY-15. Written for sites that ship more than one locale, any of which may be the primary one and any of which may be right-to-left.

## Ground rules

1. Set `<html lang="xx" dir="rtl|ltr">` per locale in the layout, driven by the locale's configuration, never by a hard-coded language list inside components.
2. Never assume the primary locale is the root path, the first locale in the list or the left-to-right one. The site's configuration names the primary; routes, `dir`, fonts, alternate links and sitemaps derive from it.
3. A missing translation falls back to the primary locale's content by rewriting at the server or build, not by redirecting the visitor.
4. Write every style with logical properties from the start. Retrofitting is slower than starting right.

## Logical-property cheat sheet

| Physical (avoid) | Logical (use) |
|---|---|
| `margin-left` / `margin-right` | `margin-inline-start` / `margin-inline-end` |
| `padding-left` / `padding-right` | `padding-inline-start` / `padding-inline-end` |
| `left: 0` / `right: 0` | `inset-inline-start: 0` / `inset-inline-end: 0` |
| `text-align: left` | `text-align: start` |
| `float: left` | `float: inline-start` |
| `border-left` | `border-inline-start` |
| `border-top-left-radius` | `border-start-start-radius` |
| `width` / `height` (in flow) | `inline-size` / `block-size` |
| `margin-top` / `margin-bottom` | `margin-block-start` / `margin-block-end` |

With a utility framework use the logical variants (`ms-4`, `me-4`, `ps-4`, `text-start`) and ban the physical ones in lint.

Things that logical properties do NOT fix:

- transforms (`translateX`) and gradients (`to right`) - use the sign token below;
- background positions and `object-position`;
- the direction of an animation or a scroll container's start edge;
- icons that point somewhere (arrows, chevrons, "back").

## The sign token

```css
:root { --dir: 1; }
:root[dir="rtl"] { --dir: -1; }

.chip__arrow { transform: translateX(calc(var(--dir) * 4px)); }
.chip:hover .chip__arrow { transform: translateX(calc(var(--dir) * 8px)); }
```

For icons that must flip, use `transform: scaleX(var(--dir))` on the icon only. Do not flip logos, checkmarks, play/pause glyphs, clocks, digits or anything depicting a real-world object with a fixed handedness.

## Full-bleed in both directions

- Prefer the named-line grid (LAY-1): the full-bleed track is symmetric by construction.
- If you must break out of a centred column, use the same value on both inline sides: `margin-inline: calc(50% - 50vw)` with `overflow-x: clip` on an ancestor that spans the page.
- Avoid `position: relative; left: 50%; margin-left: -50vw;`. It is a one-sided trick: in RTL the container's start edge is on the right and the section lands half a viewport off.
- Never use `width: 100vw` alone; it includes the scrollbar width and creates horizontal scroll on platforms with classic scrollbars.

## Marquees and tickers

The standard loop duplicates its content once and animates the track by `-50%`:

```css
.marquee { overflow: clip; }
.marquee__track {
  display: flex;
  width: max-content;
  direction: ltr;                      /* the loop maths assumes the track grows to the right */
  animation: marquee 40s linear infinite;
}
:root[dir="rtl"] .marquee__track { animation-direction: reverse; }  /* only if the content should travel the other way */
@keyframes marquee { to { transform: translateX(-50%); } }
@media (prefers-reduced-motion: reduce) { .marquee__track { animation: none; } }
```

Notes:

- The track is forced left-to-right, so every item that holds RTL text needs its own `dir="rtl"` attribute (the page's direction is no longer inherited) and `unicode-bidi: isolate`; otherwise Arabic names inside the track read in the wrong order.
- The duplicate half is `aria-hidden="true"` and its links are `tabindex="-1"` (or contain no links).
- Pause on hover and on focus-within; offer a pause button if the motion runs longer than five seconds.

## Bidirectional isolation

- Wrap phone numbers, prices with currency symbols, e-mail addresses, order codes, URLs and Latin brand names inside RTL sentences in `<bdi>` (or `dir="ltr"` for a block).
- Inputs for phone, e-mail and codes get `dir="ltr"` and `text-align: start` is fine; the value keeps its natural order.
- Choose one digit system per locale (Western or Eastern Arabic digits) and use it everywhere: copy, prices, dates, form masks.
- Punctuation at the end of a Latin phrase inside RTL text jumps to the wrong side unless the phrase is isolated.

## Typography for cursive scripts

- Line height: roughly 1.7-1.85 for body, 1.3-1.4 for headings. Diacritics and tall letterforms need it.
- `letter-spacing: 0` and no `text-transform` under `:lang(ar)`; tracking breaks joins and upper-casing does not exist.
- Load the script's own face. Pair it with the Latin face by eye and with `size-adjust` so the same `font-size` reads as the same size.
- Check heading weights: many Arabic faces have fewer weights than their Latin partner, and a missing weight is synthesised.
- Use `font-synthesis: none` and verify with `document.fonts.check(...)` for the exact weight used.

## Optical centring

A glyph centred in a box by `place-items: center` can still look off-centre because advance width and side bearings are not the ink. Measure the ink, then correct with a token:

```js
function inkOffset(text, font) {
  const ctx = document.createElement('canvas').getContext('2d');
  ctx.font = font;                               // e.g. '700 24px "Family"'
  const m = ctx.measureText(text);
  const inkLeft = -m.actualBoundingBoxLeft;      // ink edges relative to the origin
  const inkRight = m.actualBoundingBoxRight;
  const boxCentre = m.width / 2;
  const inkCentre = (inkLeft + inkRight) / 2;
  return boxCentre - inkCentre;                  // positive: shift the glyph right by this many px
}
```

For the vertical axis do the same with `actualBoundingBoxAscent` and `actualBoundingBoxDescent` against the line box. When the glyph is an SVG, read its path bounds with `getBBox()` and compare to the viewBox. Put the resulting nudge in a token (`--optical-x`), mirrored by the sign token in RTL, and confirm with a screenshot.

## Language switch

- Link to the equivalent of the CURRENT page in the other locale, not the home page.
- Mark the current language (`aria-current="true"`) and give each option its own `lang` and `hreflang` attribute so assistive technology pronounces it correctly.
- Never auto-redirect a visitor by browser language; offer the switch (search engines need every version reachable).

## Third-party widgets

Booking, chat, map and payment widgets are usually left-to-right only. Keep the page around them in the page's own direction, give the widget's container an explicit `dir="ltr"` only if the widget itself breaks, and test its box at 320px in every locale.

## Test checklist

- [ ] Build output, not only the dev server, in each locale at 320, 390 and 1440 (CSS-3 explains why).
- [ ] No horizontal overflow in RTL (`document.documentElement.scrollWidth === innerWidth`).
- [ ] Arrows, chevrons and progress directions point the reading direction.
- [ ] Marquee loop has no gap and starts from the correct edge.
- [ ] Phone numbers and prices read in the right order inside Arabic sentences.
- [ ] Focus order follows the visual (mirrored) order.
