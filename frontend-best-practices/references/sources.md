# Sources for frontend-best-practices

Every external claim in `SKILL.md` and the references, with the URL and the date it was last checked.

Status codes:

- **V** - verified: the page was fetched on the `checked` date and the claim matches its wording.
- **E** - checked earlier (2026-09-27) against the primary source and not re-fetched since. Re-check on the next refresh.
- **U** - unverified or secondary; treat as lower confidence.

Refresh procedure: when `last_verified` in `SKILL.md` is older than 30 days, re-fetch every `[fast]` source below, compare the quoted claim, update the rule and this table, then update `last_verified`.

| ID | Source | URL | Supports | Checked | Status |
|---|---|---|---|---|---|
| F1 | W3C, WCAG 2.2 | https://www.w3.org/TR/WCAG22/ | 2.2.2 Pause, Stop, Hide (auto-moving content over 5 s needs a mechanism); 2.5.8 Target Size (Minimum), 24 CSS px; 2.4.11 Focus Not Obscured (Minimum); 1.4.3 contrast 4.5:1 and 3:1 for large text; 1.4.4 resize to 200%; 1.4.10 reflow at 320 CSS px | 2026-10-03 | V |
| F2 | W3C, ARIA Authoring Practices, Carousel pattern | https://www.w3.org/WAI/ARIA/apg/patterns/carousel/ | Rotation control is the first Tab stop; rotation stops on keyboard focus and on hover; slide names such as "3 of 10"; control label changes with the action | 2026-10-03 | V |
| F3 | web.dev, Web Vitals | https://web.dev/articles/vitals | LCP up to 2.5 s, INP up to 200 ms, CLS up to 0.1, at the 75th percentile | 2026-10-03 | V |
| F4 | web.dev, prefers-reduced-motion | https://web.dev/articles/prefers-reduced-motion | `reduce` and `no-preference` values; remove animation for users who ask | 2026-10-03 | V |
| F5 | web.dev, Large, small and dynamic viewport units | https://web.dev/blog/viewport-units | `100vh` overflows under dynamic toolbars; `svh`, `lvh`, `dvh` definitions | 2026-10-03 | V |
| F6 | web.dev, Font best practices | https://web.dev/articles/font-best-practices | Self-host, WOFF2 only, `unicode-range` subsetting, `font-display` values, `size-adjust` to reduce layout shift | 2026-10-03 | V |
| F7 | MDN, `overflow` | https://developer.mozilla.org/en-US/docs/Web/CSS/overflow | `overflow: clip` does not create a scroll container; `hidden` does | 2026-10-03 | V |
| F8 | MDN, CSS scroll-driven animations and `view()` | https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timeline/view | Not Baseline at the check date; detect with `@supports`. Check the page's compatibility table for the current engine list | 2026-10-03 | V (limited: the compatibility table was not readable; Firefox status comes from an earlier project observation) |
| F9 | Tailwind CSS, Detecting classes in source files | https://tailwindcss.com/docs/detecting-classes-in-source-files | Source files are scanned as plain text; ignored: `.gitignore`d files, `node_modules`, binaries, CSS, lock files; exclude with `@source not` | 2026-10-03 | V |
| F10 | MDN, `inert` | https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/inert | `inert` disables focus, pointer events and assistive-technology access for the subtree | 2026-10-03 | V |
| F11 | Nielsen Norman Group, Hamburger menus and hidden navigation | https://www.nngroup.com/articles/hamburger-menus/ | Hidden main navigation cuts discoverability by almost half on desktop | 2026-10-03 | V |
| F12 | Nielsen Norman Group, Sticky headers | https://www.nngroup.com/articles/sticky-headers/ | Keep headers small; a partly persistent header should animate fully into view once the user scrolls up (about 300-400 ms) | 2026-10-03 | V |
| F13 | Lighthouse CI, configuration (`assert`) | https://github.com/GoogleChrome/lighthouse-ci/blob/main/docs/configuration.md | `aggregationMethod` values `median`, `optimistic`, `pessimistic`, `median-run`; the default is `optimistic` | 2026-10-03 | V |
| F14 | Playwright, Visual comparisons | https://playwright.dev/docs/test-snapshots | `toHaveScreenshot()`; baselines differ by browser, platform and headless mode, so generate them where the tests run. The `animations: 'disabled'` option is documented in the assertions API: https://playwright.dev/docs/api/class-pageassertions | 2026-10-03 | V for baselines; E for the `animations` option |
| F15 | Design Tokens Community Group, format specification | https://www.designtokens.org/ | A stable, vendor-neutral format for design tokens (stable release reported as 2025.10) | 2026-09-27 | U (page could not be fetched on 2026-10-03) |
| F16 | MDN, CSS logical properties and values | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values | `margin-inline-start`, `inset-inline-end`, `text-align: start`, `float: inline-start`, logical border radii | 2026-09-27 | E |
| F17 | MDN, `font-synthesis` | https://developer.mozilla.org/en-US/docs/Web/CSS/font-synthesis | `font-synthesis: none` stops faux bold and italic | 2026-09-27 | E |
| F18 | MDN, `isolation` | https://developer.mozilla.org/en-US/docs/Web/CSS/isolation | `isolation: isolate` creates a stacking context | 2026-09-27 | E |
| F19 | MDN, Intersection Observer API | https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API | `threshold`, `rootMargin` semantics used by the reveal pattern | 2026-09-27 | E |
| F20 | web.dev, Optimize Largest Contentful Paint; Optimize CLS | https://web.dev/articles/optimize-lcp and https://web.dev/articles/optimize-cls | Eager, high-priority LCP image; explicit dimensions; do not lazy-load the LCP image | 2026-09-27 | E |
| F21 | Smashing Magazine and CSS-Tricks on fluid type and full-bleed layouts | https://www.smashingmagazine.com/ and https://css-tricks.com/ | `clamp()` fluid type mixing rem and `vw`; grid-based full-bleed layouts | 2026-09-27 | U (general reading list, no single page) |

## Claims that come from experience, not a source

These are lessons from real builds. They are logged with dates in `LESSONS.md` and are not independently documented:

- "Component stylesheets are appended at the end of `<head>`" (framework behaviour; confirm on your framework version).
- "A CSS mask image moves `load`" (observed in lab tests; the mechanism is that a stylesheet-referenced resource is part of the load event).
- "A scoped-style compiler appends its attribute to every compound selector" (observed with one framework; the principle applies to any attribute-scoping compiler).
- "Box-centred glyphs can look off-centre" (optical, measured per font).
