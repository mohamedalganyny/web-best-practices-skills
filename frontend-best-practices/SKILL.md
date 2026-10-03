---
name: frontend-best-practices
description: Use at the start of any new website, landing page, redesign or front-end review, and again before calling the page done. A checklist of layout, RTL and bilingual, motion and accessibility, performance and caching, CSS pipeline, component and testing rules, each with the reason it exists. It updates itself - it re-checks fast-moving guidance when it is stale and logs new lessons after every use. Pair it with seo-best-practices for any public, indexable site.
last_verified: 2026-10-03
related: seo-best-practices
---

# Frontend best practices

`last_verified: 2026-10-03` - the date every `[fast]` rule below was last re-checked against its source (see `references/sources.md`).

A working checklist for building public websites from a design: measurable fidelity, robust layout in every language direction, motion that never hurts, fast and correctly cached delivery, and tests that look at the real result. Every rule is one or two lines plus the reason it exists, so you can judge when an exception is safe.

## When to call this skill

Call it:

- before you design or scaffold a new website, landing page or marketing page, and before you write a brief for someone else to build one;
- before a redesign, or when implementing from a design handoff (prototype, mock-up, brand guide);
- when you review a front-end change, or when a layout, right-to-left, animation, caching or font bug appears (the fix usually has siblings: grep for them);
- before you call a page done, and again before launch.

Do not call it for back-end-only work, native apps or a throwaway spike. For dashboards and admin screens use only the layout, accessibility and testing groups; the motion and marketing-page rules assume a public page.

Call `seo-best-practices` as well whenever the pages will be public and indexed: it owns URLs, titles, headings, structured data, sitemaps, locale routing, consent and tags, and AI-search readiness. This skill owns how the page is built, styled, animated, cached and tested.

## Self-update protocol (follow it every time you use this skill)

1. **Check freshness.** Read `last_verified` above. If it is older than 30 days (or missing), re-check every rule tagged `[fast]` (browser support, accessibility criteria, performance thresholds, framework and tool behaviour) with a web search or by fetching the source listed in `references/sources.md`. Update the rule text, the source's `checked` date in that file, and finally `last_verified` - set it to today only when all `[fast]` rules were re-checked. If you cannot reach the web, say so in your answer, change no dates and treat the `[fast]` rules as unconfirmed.
2. **Read `LESSONS.md` before you start** and apply every lesson newer than your last use. Even when `last_verified` is fresh, check support for any new browser feature you are about to depend on.
3. **After the work, append new lessons to `LESSONS.md`**: date, symptom, cause, rule (and the rule ID). Add only lessons that generalise - ask whether a stranger on a different project would hit the same thing. If a lesson changes or contradicts a rule, edit the rule here, note the old wording in the lesson ("Replaces: ...") and add a line to `CHANGELOG.md`.
4. **Save a one-line pointer to the agent's memory** (if it has persistent memory): the skill name, where it lives, and when to call it. Example: "Skill `frontend-best-practices` is installed; call it when building, redesigning or reviewing any website; companion `seo-best-practices`." Update the pointer instead of adding a second one.
5. **Never store secrets, client names, private URLs, internal paths or personal data** in this skill, its lessons or its memory pointer. Describe the situation neutrally ("an agency site", "a clinic site in Arabic and English").

## Before you start

- [ ] Run the self-update protocol (steps 1 and 2).
- [ ] List the locales, which one is primary (never assume it is the first or the Latin one), the text direction of each, and the fonts that cover each script.
- [ ] Find the design's source of truth (prototype, brand guide, supplied logo and font files) and confirm which files actually exist.
- [ ] Map every section as contained, breakout or full-bleed, and note the frame widths the designer drew.
- [ ] Define tokens (colour, type scale, spacing, radii, widths, easings, durations) and the performance budget (LCP, CLS, script weight). List every third party.
- [ ] Learn the pipeline facts that bite later: where the framework puts component CSS in `<head>`, what the CSS scanner reads, how assets are hashed, what a deploy replaces or never deletes.
- [ ] Decide which checks run on the BUILT output, and which mechanism you will break on purpose to prove its test.
- [ ] Public pages: open `seo-best-practices` for URL structure, locale routing, schema and consent before writing templates.

## 1. Layout and RTL

- **LAY-1 Classify every section as contained, breakout or full-bleed in its spec, and build on a named-line grid:** `[full-start] minmax(var(--gutter),1fr) [content-start] min(var(--max),100%) [content-end] minmax(var(--gutter),1fr) [full-end]`. Why: a default content wrapper boxes the hero and logo strip ("space on both sides"). A full-bleed background can still hold content aligned to the `content` lines.
- **LAY-2 Never bleed with `width: 100vw`.** Use the grid track, or equal inline margins (`margin-inline: calc(50% - 50vw)`) with `overflow-x: clip` on the page. Why: `100vw` includes the scrollbar and creates horizontal scroll; `left: 50%` plus a physical `margin-left` is not symmetric and breaks in RTL.
- **LAY-3 One gutter token** (`--gutter: clamp(16px, 4vw, 48px)`); phones never get less than 16px. Why: ad-hoc paddings are how edges stop lining up.
- **LAY-4 Container queries for components, a few content-driven breakpoints for pages.** Why: a component that answers to its own slot works wherever it is placed.
- **LAY-5 Kill mystery bands.** A section's first and last children get `margin-block: 0` (or the section is `display: flow-root`). Why: collapsed margins paint a stripe under the header.
- **LAY-6 Decorative shapes** are `aria-hidden="true"`, `pointer-events: none`, below the content in z-order, and their section has `isolation: isolate; overflow: clip`. Why: an unconstrained shape paints over text and spills into the next section.
- **LAY-7 `[fast]` Heroes use `min-height: 100svh`** (`dvh` only when it must track the toolbar), never `100vh` on mobile; `viewport-fit=cover` always comes with `env(safe-area-inset-*)` padding. Why: `100vh` bleeds out of the viewport under dynamic toolbars. `[F5, 2026-10-03]`
- **LAY-8 `<html lang dir>` per locale; logical properties everywhere** (`margin-inline-start`, `inset-inline-end`, `text-align: start`; no `left`/`right`). Why: physical properties cause most RTL bugs. See `references/rtl-and-bilingual.md`.
- **LAY-9 Mirror directional icons and motion with a sign token** (`--dir: 1; [dir=rtl] { --dir: -1 }`, then `translateX(calc(var(--dir) * 12px))`); never mirror logos, checkmarks, media controls or digits. Why: one switch beats per-component overrides.
- **LAY-10 A marquee or ticker track sets `direction: ltr`;** reverse its animation deliberately for RTL and keep each item's own text direction. Why: the doubled-track loop (`translateX(-50%)`) assumes the track extends to the right; in an RTL container it starts on the wrong side and shows a gap.
- **LAY-11 Wrap phone numbers, prices, e-mails and codes in `<bdi>` or `dir="ltr"` inside RTL text;** pick one digit system per locale. Why: the bidi algorithm moves `+`, spaces and punctuation.
- **LAY-12 Cursive scripts (Arabic and similar):** looser leading (about 1.7-1.85 body, 1.3-1.4 headings), `letter-spacing: 0` and no `text-transform` under `:lang(ar)`. Why: tracking breaks the letter joins; balance the face's size against the Latin face with `size-adjust`.
- **LAY-13 Box-centred is not optically centred.** A glyph in a circle, an arrow or a single letter can look off-centre because of side bearings. Measure the ink (canvas `actualBoundingBox*` or a pixel scan) and add a small optical-offset token. See `references/rtl-and-bilingual.md`.
- **LAY-14 Embedded third-party widgets (booking, maps, chat) are rarely RTL-aware.** Keep the page around them RTL and test the widget's own box in every locale.
- **LAY-15 Check every locale on the BUILT output,** not only the dev server (see CSS-3).

## 2. Motion and accessibility

- **MOT-1 Reveals start visible.** Content hides only under a class that JavaScript adds once its observer is ready; with no JavaScript nothing sits at `opacity: 0`. Why: hidden-until-script content fails crawlers, slow devices and print.
- **MOT-2 Make reveals actually seen.** Elements already in view at load do not animate. Add the ready class, let the hidden state paint (double `requestAnimationFrame`), then observe with `threshold: 0` and `rootMargin: '0px 0px -10% 0px'` (a 0.5 threshold never fires on tall elements). Keep the `transition` on the base element. Prove it with frames captured WHILE scrolling. Why: reveals that are wired but never visibly run are the common failure. See `references/motion.md`.
- **MOT-3 Animate only `transform` and `opacity`** (`clip-path` and `filter` sparingly); never animate the LCP element in. Why: anything else triggers layout or paint and delays the metric you are graded on.
- **MOT-4 Reduced motion removes movement** (a short fade at most): no parallax, marquee motion, smooth-scroll hijack, header hiding or autoplay. Write animations inside `prefers-reduced-motion: no-preference`, or cancel them under `reduce`, and test with emulated media. `[F4]`
- **MOT-5 Anything that moves by itself for more than 5 seconds needs a pause control** (WCAG 2.2.2); pause on hover and focus-within; static under reduced motion. `[F1]`
- **MOT-6 `[fast]` Scroll-driven CSS (`animation-timeline: view()`) goes inside `@supports` and `no-preference`, with the finished state as the default.** Why: it was not Baseline at the check date and Firefox stable lacked it when this lesson was learned. `[F8, 2026-10-03]`
- **MOT-7 Custom cursors and magnetic effects** mount only under `(hover: hover) and (pointer: fine)`, never hide the system cursor, never gate information, and keep contrast on every surface including accent floods (blend mode or a state-aware colour; test over each surface colour).
- **MOT-8 Avoid preloaders on marketing pages.** If one is mandated, the content must be complete underneath, the loader must never delay the LCP element, and it is skipped under reduced motion.
- **MOT-9 Contrast is 4.5:1 for body text and 3:1 for large text, UI and focus rings** `[F1]`. Measure in EVERY state (rest, hover, tap, focus) and every tone with the real palette. Text on an accent flood takes the colour designed for text-on-accent, never the section's ground token: a light section's ground on a lime flood measured 1.18:1.
- **MOT-10 Touch targets are 24px minimum (WCAG 2.5.8), aim for 44px, with 8px between neighbours.** `[F1]`
- **MOT-11 Visible `:focus-visible` ring, a skip link first, one `<h1>` and an ordered outline;** `html { scroll-padding-top: var(--header-h) }` so anchors and focused elements never land under a sticky header (WCAG 2.4.11). `[F1]`
- **MOT-12 Reflow at 320px with no horizontal scroll; usable at 200% zoom** (WCAG 1.4.10 and 1.4.4) `[F1]`. Fluid type is `clamp(min-rem, intercept-rem + slope * vw, max-rem)` mixed in rem, with max no more than 2.5 times min.

## 3. Performance and caching

- **PER-1 `[fast]` Field targets at the 75th percentile: LCP up to 2.5 s, INP up to 200 ms, CLS up to 0.1.** `[F3, 2026-10-03]` Set a stricter lab budget if you like, and make the CI assertion pessimistic: Lighthouse CI aggregates optimistically by default (the best run counts), so an unqualified budget is decorative. `[F13, 2026-10-03]`
- **PER-2 Hashed assets are `Cache-Control: public, max-age=31536000, immutable`; HTML is `no-cache`;** purge the CDN's HTML cache after every deploy. Why: a CDN can hold the old page for hours.
- **PER-3 A fixed-name CSS, JS or font-CSS file under a year-long `immutable` cache is stale forever for anyone who has visited once.** Fingerprint every such file at build (rename with a content hash, rewrite the references), keep the list explicit and fail the build on an unhashed asset that is not on it. Why: returning visitors never see an override or fix. See `references/caching-and-deploys.md`.
- **PER-4 Photos:** AVIF/WebP in 3-5 widths, `srcset` with an honest `sizes`, `width` and `height` on every image, `loading="lazy" decoding="async"` below the fold; the LCP image is eager, `fetchpriority="high"` and never faded in.
- **PER-5 Logos and icons are SVG with a `viewBox`;** a raster logo is exported at 2x its largest rendered size, no more.
- **PER-6 Media is copied into the build output.** A public page must not request the CMS or an API for images at runtime. Why: an access-controlled origin returns a well-formed 403 that every text check passes and browsers draw as nothing.
- **PER-7 `[fast]` Fonts:** self-host WOFF2, subset per script with `unicode-range`, preload only the one or two critical files, `font-display: swap` with a metric-matched fallback (`size-adjust`), load every weight the design uses and set `font-synthesis: none`. Why: a missing weight is faked and reads as "wrong font". `[F6, 2026-10-03]`
- **PER-8 Third parties sit behind facades** (video click-to-load, chat on first interaction); no task over 50 ms on scroll or tap.
- **PER-9 A file referenced from a stylesheet (mask image, background, font) is a network request that moves `load`.** Budget it, and preload it if it is critical.
- **PER-10 Deploy hygiene.** A renamed route needs a redirect AND removal (or a stub) of the old file; list a hosting root before any whole-root replacement; test redirects live, including a nested child path. See `references/caching-and-deploys.md`.

## 4. CSS pipeline

- **CSS-1 An override stylesheet must come after the framework's own CSS.** Some frameworks append component stylesheets at the END of `<head>`, so a `<link>` written earlier loses every equal-specificity tie. Test the ORDER in the built HTML and compare screenshots at the widest size.
- **CSS-2 Scope every global style to its component root** (`:where(.root) .x` or `@scope`; `:where` adds no specificity). Why: an unscoped rule in a global style ships to every page and restyles every component that shares the class name. Prove it by measuring the untouched component in a browser.
- **CSS-3 Scoped-style compilers append their attribute to every compound selector.** Styling an ancestor (`[dir=rtl] svg`), a child component's root element or slotted content needs the global escape (`:global()` or equivalent), otherwise the rule is dead with no error. The dev server can render it correctly while the build does not: check the build.
- **CSS-4 `[fast]` Utility-class scanners read every non-ignored file as plain text.** Test fixtures, docs, comments and ordinary words (an event name, `ease-out` in a transition) can become shipped CSS. Exclude test and doc folders (`@source not` in Tailwind v4) and grep the built CSS for utilities you never wrote. `[F9, 2026-10-03]`
- **CSS-5 A component must not read the file system at render time.** It works in dev and in a test container and throws (or misses the file) in the bundled build. Import through the bundler or share a plain constant.
- **CSS-6 Never leave internal notes in HTML comments in templates:** compilers can emit them verbatim into the page. Use the language's own comment syntax, and add a build check that fails on `<!--` and on internal phrases (paths, tool names).
- **CSS-7 A shared engine never knows a client's name.** No `slug === 'x'` branches; differences are settings or declared overrides, and a test fails any slug comparison. A variant is a few tokens, not a component; more than about three overrides on one site means the engine lacks a variant.
- **CSS-8 Reset the browser's italics where the design has none** (`var, em, i, cite, dfn, address { font-style: normal }`); counters get `font-variant-numeric: tabular-nums`, and a number plus its suffix gets `white-space: nowrap`. Why: count-up numbers rendered in italics; "5 / M+" broke across lines.
- **CSS-9 Tokens before components.** Colour, type scale, spacing, radii, container widths, easings and durations first (a design-tokens file), so no value is ever eyeballed twice.

## 5. Components

- **CMP-1 The mobile header is a grid** (`auto 1fr auto`, `min-width: 0` on the middle) tested at 320px. Only the logo and the menu button stay visible; the secondary call to action and the language switch move into the menu.
- **CMP-2 Desktop shows up to about five links and no hamburger.** Hidden navigation cuts discoverability roughly in half. `[F11]`
- **CMP-3 Hide-on-scroll is direction-aware:** hide only on scroll DOWN past about two header heights with a 5-10px delta; show IMMEDIATELY on any upward scroll; always show near the top, while the menu is open and while keyboard focus is inside (`:focus-visible` within, not `:focus-within`, which a tap on the toggle leaves pinned); use `transform` only; never hide under reduced motion. `[F12]`
- **CMP-4 Scroll listeners** go on the element that actually scrolls, are `passive`, rAF-throttled and read no layout.
- **CMP-5 Prefer `position: sticky` to `fixed` plus compensation padding,** and never put `overflow: hidden` on an ancestor of a sticky header (use `overflow: clip`; `hidden` creates a scroll container and sticky silently stops). `[F7]`
- **CMP-6 Mobile menu:** a real `<button aria-expanded aria-controls>`, Escape closes it and returns focus to the button, and the page behind is `inert`. `[F10]`
- **CMP-7 Carousels follow the APG pattern:** pause/play is the first stop in the tab order, rotation stops when focus enters or on hover and does not restart on its own, off-screen slides are `inert`, no autoplay under reduced motion, stop on any interaction, swipe via scroll-snap, each slide labelled ("3 of 10"). `[F2]` See `references/carousels.md`.
- **CMP-8 Logo walls:** monochrome at rest, colour on hover and focus; size by visual area, not `max-height`; `alt` is the company name; duplicated marquee tracks are `aria-hidden="true"`.
- **CMP-9 Supplied brand files are not trustworthy until rendered.** Check each logo is real vector paths (some "vector" files embed a raster or use a fallback font), take colours from the guide's palette page rather than the files, and compose any missing lockup from the real paths, measuring bounds in a browser. Raster-only: trace (single colour or multi-colour) and compare side by side; a PDF that holds vectors should be extracted, not traced.
- **CMP-10 Favicon from the brand icon:** SVG plus PNGs (32, 48, 180, 192, 512) with transparent rounded corners. A screenshot adds a background, so cut the corners with an alpha mask.
- **CMP-11 A client's real deliverables beat generated images.** Use AI imagery only for concepts, never as someone's work, results or people. Put ad-account numbers in text; never publish the screenshots (other campaigns, account ids, budgets).
- **CMP-12 The language switch links to the equivalent of the CURRENT page in the other locale,** not the home page, and follows the design's own control.

## 6. Testing and verification

- **TST-1 The design's own values are the spec.** Render the prototype in a browser, read `getComputedStyle` for every text role and box (family, size, weight, line-height, letter-spacing, case, colour, padding), read the same from your build and diff. A difference of 2px or more, or any family, weight, case or colour mismatch, is a bug. Why: eyeballed values drift. See `references/verification.md`.
- **TST-2 Measure at the design's own frame widths first** (often 1440 and 390), then 320, 768, 1280, 1920, 2560. Parity is provable only where the designer drew; the rest is interpolation.
- **TST-3 Side-by-side screenshots (design | build) at a phone and a wide width in every locale, and LOOK at them.** A screenshot saved but not examined hid a contrast failure that was visible in it.
- **TST-4 `[fast]` Visual regression:** `toHaveScreenshot()` with animations disabled, fixed viewports and per-platform baselines in the repository. Text-based tests cannot see layout. `[F14, 2026-10-03]`
- **TST-5 Verify a guard actually fetches.** A guard that reads HTML as text passes on a well-formed, dead URL. When the claim is that a page WORKS, load it and read the network log: zero 4xx/403/ORB, no third party the page did not ask for, fonts and media served from the site. A page scored 100 on best-practices while its only image was blocked.
- **TST-6 A measurement taken "just after load" on an animated page measures a moment, not a state.** Read styles at `load` (not `domcontentloaded`), poll until the value settles instead of sleeping a fixed time that ignores staggers, and remember that a CSS mask image or font moves `load` (PER-9).
- **TST-7 Prove the font rendered:** `document.fonts.check('700 1rem "Family"')`. A wrong family name falls back silently.
- **TST-8 Break every new mechanism once on purpose,** confirm its test goes red, restore it. Read the failed-FILES line too: a failing `beforeAll` reports its tests as "skipped".
- **TST-9 When a test fails, run it on the clean committed code before blaming your change.** Pre-existing failures eat hours otherwise.
- **TST-10 Headless Chromium is not Safari.** Test on a real phone before launch. A CDN may challenge headless browsers while serving real ones and search crawlers: verify the live site with `curl` and a crawler user agent, and drive live forms with a request.
- **TST-11 Grep for siblings** before you finish a fix: a fix on one component, route or locale usually has two neighbours.

## Before you call it done

Run every item on the BUILT site, in every locale:

- [ ] Computed-style diff against the design: nothing 2px or more off, no family, weight, case or colour mismatch.
- [ ] Screenshots, design | build, at 320, 390, 768, 1280, 1440, 1920 and 2560, in each locale - and looked at.
- [ ] No horizontal overflow at any width (`scrollWidth === innerWidth`).
- [ ] Header: nothing overlaps at 320; hides on scroll down; returns on scroll up; stays while the menu is open or keyboard focus is inside.
- [ ] Motion on: reveals caught mid-animation in scroll frames. Motion off (reduced motion, and no JavaScript): everything visible and static.
- [ ] `document.fonts.check` is true for every family and weight used.
- [ ] Network: zero 4xx/403/ORB; no unrequested third party; fonts and media from the site.
- [ ] Contrast measured in every state and tone; touch targets 24px or more.
- [ ] Console clean; keyboard-only walk-through; 200% zoom.
- [ ] CI performance budget green with a pessimistic assertion; fixed-name assets fingerprinted; CDN purged after deploy.
- [ ] A real phone (iOS Safari) before launch.
- [ ] New lessons appended to `LESSONS.md`; memory pointer saved.

## Related skill: seo-best-practices

Call `seo-best-practices` when the pages will be indexed, in any of these moments:

- planning URLs, headings, titles, internal links or locale routing (hreflang, canonical, sitemaps) - they constrain your templates and router;
- adding structured data, a consent banner, analytics or ad tags - they affect LCP, CLS and what loads before consent;
- renaming routes, migrating a site or deploying file by file - redirects and stale pages are an SEO problem first;
- launching: rendering for crawlers and AI search, robots.txt, and verification.

`seo-best-practices` calls this skill back for Core Web Vitals work, RTL layouts, carousels and any template that must stay static HTML.

## References

- `references/rtl-and-bilingual.md` - logical-property cheat sheet, sign token, marquee direction, bidi isolation, Arabic type, optical centring.
- `references/motion.md` - the reveal pattern, hide-on-scroll logic, reduced motion, scroll-driven fallbacks, cursor and preloader rules.
- `references/carousels.md` - an accessible carousel, step by step, with its tests.
- `references/caching-and-deploys.md` - cache headers, fingerprinting, CDN purge, redirects, shared roots, DNS.
- `references/css-pipeline.md` - ordering, scoping, scanners, render-time file access, comments.
- `references/verification.md` - the measurement method, screenshot matrix, network audit, break-on-purpose.
- `references/sources.md` - every external claim with its URL and the date it was checked.
- `LESSONS.md` - the dated, append-only lessons log.
