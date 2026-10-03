# Verification: measure, look, fetch, break

Supports TST-1 to TST-11. A page that says the right things is not a page that works, and a screenshot nobody examined is not evidence. This file is the method.

## 1. Measure the design instead of eyeballing it

When the design is an HTML prototype or a handoff export, it is a specification you can query. Load it in a browser and read the computed style of every role: section, heading levels, paragraph, label, chip, button, stat, logo cell.

```js
const PROPS = [
  'fontFamily', 'fontSize', 'fontWeight', 'lineHeight', 'letterSpacing', 'textTransform',
  'color', 'backgroundColor', 'paddingTop', 'paddingBottom', 'paddingInlineStart', 'paddingInlineEnd',
  'marginTop', 'marginBottom', 'gap',
];

export function harvest(selectors) {
  return Object.fromEntries(selectors.map((sel) => {
    const el = document.querySelector(sel);
    if (!el) return [sel, null];
    const cs = getComputedStyle(el);
    const r = el.getBoundingClientRect();
    return [sel, { ...Object.fromEntries(PROPS.map((p) => [p, cs[p]])), width: r.width, height: r.height }];
  }));
}
```

Run it on the prototype and on your build at the same viewport and diff:

- numeric values: a difference of 2px or more is a bug;
- `fontFamily`, `fontWeight`, `textTransform`, `color`: any mismatch is a bug.

Do this at the design's own frame widths first (often 1440 and 390). Everything between is interpolation, and parity is provable only where the designer drew.

Then turn the harvested values into tokens before you build more components. A value nobody types by hand cannot drift.

## 2. The screenshot matrix

| Widths | 320, 390, 768, 1280, 1440, 1920, 2560 |
|---|---|
| Locales | every shipped locale, RTL included |
| States | rest, menu open, a hover, a focus ring, reduced motion |

- Capture design | build side by side at a phone width and a wide width, in every locale.
- LOOK at them. Read the image. State one concrete observation per screenshot. In one real case a contrast failure was visible in the very screenshot the implementer had saved and called fine.
- Visual regression: `expect(page).toHaveScreenshot()` with animations disabled, fixed viewport sizes, and baselines committed per platform (rendering differs by OS, browser and headless mode, so generate baselines where the tests run). Text-based assertions cannot see layout.

## 3. No horizontal overflow

```js
const overflow = await page.evaluate(() => document.documentElement.scrollWidth - innerWidth);
expect(overflow).toBe(0);
```

At every width in the matrix, in every locale. The usual culprits are `100vw` bleeds, a marquee without `overflow: clip`, decorative shapes without containment, and long unbroken strings (set `overflow-wrap: anywhere` on user content).

## 4. Fonts and styles at the right moment

- `document.fonts.check('700 1rem "Family"')` for every family and weight used. A wrong family name falls back silently.
- Read styles after `load`, not `domcontentloaded`. Poll for the settled value (`expect.poll`) rather than sleeping a fixed time: staggers, font swaps and transitions each add their own delay.
- A test that measures "just after load" on an animated page measures one moment of a moving thing. Assert the end state, or assert the moment in an explicit motion test (see `motion.md`).
- A stylesheet-referenced file (mask image, font, background) is a network request that holds `load`. A test that used to pass at `load` can start measuring too early or too late after you add one.

## 5. Contrast in every state

Compute the ratio with the real palette, in every state and tone: rest, hover, tap, focus, on each section tone (light, dark, accent). One sweep in code beats a screenshot:

```js
const lum = ([r, g, b]) => {
  const f = (c) => { c /= 255; return c <= 0.03928 ? c / 12.92 : ((c + 0.055) / 1.055) ** 2.4; };
  return 0.2126 * f(r) + 0.7152 * f(g) + 0.0722 * f(b);
};
export const ratio = (a, b) => {
  const [hi, lo] = [lum(a), lum(b)].sort((x, y) => y - x);
  return (hi + 0.05) / (lo + 0.05);
};
```

Thresholds: 4.5 for body text, 3 for large text, UI components and focus rings. Text over an image is measured at its worst point.

## 6. Verify a guard actually fetches

Guards over built HTML read it as text. They see that a reference is spelled correctly. They cannot see that the URL is dead, blocked or unauthorised. A page can score 100 in an automated best-practices audit while its only image is blocked.

When the claim is that the page WORKS:

1. Load the built page in a real browser (local build, not a CDN that challenges headless browsers).
2. Record every request. Assert zero 4xx, zero 403, zero blocked-by-ORB, no third party the page did not ask for, fonts and media served from the site's own origin.
3. Assert the visible result: the LCP image has non-zero natural size, the font check passes, the form posts and receives a 2xx.

An exemption granted to one mechanism (for example "this origin is allowed in the identity check") settles identity only. Say what the exemption does NOT cover (delivery) in the same place you grant it.

## 7. Break it on purpose

For every new mechanism (a guard, a fingerprinting step, a reveal, a redirect rule, a test helper):

1. Write the test first and watch it fail for the right reason.
2. Make it pass.
3. Break the mechanism deliberately - delete the line, flip the condition, remove the file - and confirm the test goes red.
4. Restore it.

A test that has never failed proves nothing. Read the failed-FILES line of the runner as well as the failed-tests line: an exception in `beforeAll` shows its tests as "skipped", not failed.

## 8. Before you blame your change

When a test fails after your edit, run the same test on the clean, committed code first (stash nothing; check out the base in another folder or use a fresh clone). If it fails there too, it is a pre-existing failure and you have just saved an hour of looking at the wrong diff.

## 9. Siblings

After a fix, grep for the pattern in every collection, route, component and locale. A bug in one carousel is usually in the next one; a missing logical property in one card is in its twin.

## 10. Real devices

- Headless Chromium is not Safari. Test on a real phone before launch, with a keyboard-only pass and a screen-reader spot check on the main flows.
- Zoom to 200%, enable the OS reduced-motion setting, and try a slow network profile once.
