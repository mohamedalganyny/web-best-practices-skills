# Lessons log - frontend-best-practices

Append-only. Newest entries go at the BOTTOM of the log. Never edit or delete an old entry; if a lesson turns out wrong, add a new entry that says so and quote what it replaces.

## How to add a lesson

Add one only if a stranger on a different project could hit the same thing. Use this shape:

```
### YYYY-MM-DD · Short title
- Symptom: what was seen (observable, not interpreted).
- Cause: the actual mechanism, once understood.
- Rule: the rule that prevents it (give the rule ID in SKILL.md, or the new rule you added).
- Replaces: (optional) older wording this corrects.
```

Never include client or company names, private URLs, internal paths, ticket numbers, keys or personal data. Describe the situation neutrally.

Entries marked `(seed)` were written on the seeding date from the originating project's notes; their date is the date they were recorded here, not necessarily the day the incident happened.

## Log

### 2026-09-27 · Values drifted because they were re-derived by eye
- Symptom: after a full first build from a design, fonts, sizes, the hero width and the logo wall all differed from the design by small but visible amounts.
- Cause: every value was re-typed from screenshots instead of read from the design's computed styles.
- Rule: TST-1, TST-2, CSS-9. Harvest computed styles from the prototype at its own frame widths, diff them against the build, and put the values in tokens first.

### 2026-09-27 · The default wrapper boxed full-bleed sections
- Symptom: a hero and a logo marquee showed empty space on both sides.
- Cause: every section was rendered inside the default content container.
- Rule: LAY-1. Classify each section (contained, breakout, full-bleed) in its spec and use a named-line grid so a background can bleed while its text aligns to the content lines.

### 2026-09-27 · Wired reveals that never visibly ran
- Symptom: scroll-reveal code existed and passed review but visitors saw no animation.
- Cause: the hidden and visible states were applied in the same frame, the observer threshold never fired for tall elements, and elements already in view animated instantly.
- Rule: MOT-2 and the double-`requestAnimationFrame` pattern in `references/motion.md`. Prove it with opacity samples taken while scrolling.

### 2026-09-27 · Override stylesheet lost every tie
- Symptom: a hero came out 90px short although the override file was "applied".
- Cause: the framework appends component stylesheets at the end of `<head>`, after the override `<link>` placed in the layout.
- Rule: CSS-1. Test the order in the built HTML and compare screenshots at the widest size.

### 2026-09-27 · A shared engine hard-coded one client
- Symptom: nine places in shared code compared the site slug to a client's name.
- Cause: a quick path to one client's look, never generalised into settings.
- Rule: CSS-7. Differences are settings or declared overrides; a guard test fails any slug comparison.

### 2026-09-27 · Small structural bugs found only by a human walk-through
- Symptom: a black band under the mobile header; a decorative shape painted over hero text; the menu button covering the call to action at 320px; a ticker number such as "5 / M+" wrapping; count-up numbers in italics; a hero mark that was a small PNG scaled up.
- Cause: collapsed margins, an uncontained decoration, a header that did not drop secondary controls on small screens, missing `nowrap`, the browser's default italics for `<var>`, a raster logo.
- Rule: LAY-5, LAY-6, CMP-1, CSS-8, PER-5.

### 2026-09-28 · Header stayed pinned after a tap
- Symptom: after opening and closing the mobile menu, the hide-on-scroll header never hid again.
- Cause: the "never hide while the header has focus" rule used `:focus-within`; closing the menu leaves focus on the toggle, which counts forever.
- Rule: CMP-3. Use KEYBOARD focus (`:focus-visible` within). Test: tap Menu, close, scroll down - hidden; scroll up 50px - visible; plus a keyboard case that stays visible.

### 2026-09-28 · Text on an accent flood failed contrast
- Symptom: on hover or tap an accent colour flooded a card and the text became unreadable (contrast 1.18:1).
- Cause: the flood took its text colour from the section's ground token, which in a light section is cream, not the colour meant for text ON the accent.
- Rule: MOT-9. Measure contrast in every state (rest, hover, tap, focus) and every tone with the real palette. The implementer's "screenshot looked at" missed it; the reviewer found it in the same screenshot.

### 2026-09-28 · Supplied "official" brand files were not trustworthy
- Symptom: two of five "vector" logo files embedded raster images and one set its text in a fallback font; the good vectors used an accent colour that differed from the brand guide.
- Cause: files were taken at face value; colours were read from the files instead of the guide.
- Rule: CMP-9. Render every supplied logo, check it is real vector paths, take colours from the guide's palette page, compose missing lockups from the real paths and measure their bounds in a browser.

### 2026-09-28 · Case-study imagery
- Symptom: a temptation to illustrate client results with generated images and to show ad-account screenshots.
- Cause: real deliverables were not requested up front.
- Rule: CMP-11. A client's real deliverables beat generated images; AI imagery only for concepts, never as someone's work, results or people. Put the numbers from ad-account screenshots in text and never publish the screenshots.

### 2026-09-28 · Favicon
- Symptom: a favicon cut from a browser screenshot had a solid background behind its rounded corners.
- Cause: a screenshot adds a background.
- Rule: CMP-10. Build from the brand icon: SVG plus PNGs (32, 48, 180, 192, 512) and cut transparent corners with an alpha mask.

### 2026-09-30 · Returning visitors never saw the override and motion fixes
- Symptom: after a fix shipped, a site looked unchanged for anyone who had visited before; fresh browsers looked right.
- Cause: files with fixed names (an override stylesheet, a motion script, a font stylesheet) were served with a year-long `immutable` cache.
- Rule: PER-3. Fingerprint every fixed-name CSS and JS file at build, keep the list explicit, and fail the build on an unhashed asset that is not on it.

### 2026-10-03 · A stray word became shipped CSS (seed)
- Symptom: dead utility classes and theme variables appeared in every client's production CSS after a test fixture and a transition declaration were added.
- Cause: the utility-class scanner reads every non-ignored file as plain text, tests and ordinary words included (an event name; `ease-out` in a component's own CSS).
- Rule: CSS-4. Exclude test and doc folders from scanning and grep the built CSS for utilities you never wrote.

### 2026-10-03 · A new component's global style broke older ones (seed)
- Symptom: every existing site's hero changed after a new hero component shipped; all text-based tests stayed green.
- Cause: unscoped `.hero-image` and `.cta` rules in a global style are bundled into every page and restyled every component with the same class names.
- Rule: CSS-2. Scope every rule to its component root with `:where(.root)`; prove it by measuring an UNTOUCHED older component in a browser.

### 2026-10-03 · The ancestor selector that matched nothing (seed)
- Symptom: an RTL mirror rule was correct in the dev server and dead in the build; the same trap hit a child component's root element a few days later.
- Cause: scoped-style compilers append their attribute to every compound selector, so selectors about an ancestor, a child's root or slotted content match nothing.
- Rule: CSS-3. Use the global escape for those selectors and check the BUILT output in every locale.

### 2026-10-03 · A component that read a file at render time (seed)
- Symptom: a build died with `ENOENT` although the dev server and every component test passed; two components shipped with the same bug.
- Cause: the bundler moved the component code and never copied the file it read at render time.
- Rule: CSS-5. Import assets through the bundler or share a constant.

### 2026-10-03 · An internal note shipped in the page (seed)
- Symptom: a path and a project note appeared in the public HTML.
- Cause: the template compiler emitted an HTML comment verbatim.
- Rule: CSS-6. Keep notes in the code fence; fail the build on `<!--` and on internal phrases.

### 2026-10-03 · Full-bleed that broke in right-to-left (seed)
- Symptom: a full-bleed section sat half a viewport off in RTL.
- Cause: `left: 50%` with a physical negative margin is a one-sided trick; the container's start edge is on the other side in RTL.
- Rule: LAY-2. Use the grid track or equal inline margins (`margin-inline: calc(50% - 50vw)`) with `overflow-x: clip`.

### 2026-10-03 · The marquee left a gap in RTL (seed)
- Symptom: the looping logo strip showed a gap and started from the wrong edge in Arabic.
- Cause: the doubled-track loop (`translateX(-50%)`) assumes the track grows to the right, and an RTL container grows to the left.
- Rule: LAY-10. Force `direction: ltr` on the track, reverse the animation deliberately, and give RTL items their own `dir`.

### 2026-10-03 · A carousel that autoplayed for everyone (seed)
- Symptom: a project slider kept moving under keyboard focus and reduced motion, with no way to pause and off-screen slides still tabbable.
- Cause: autoplay was added without the accessible-carousel pattern.
- Rule: CMP-7 and `references/carousels.md`: pause/play first in the tab order, stop on focus and interaction, no autoplay under reduced motion, `inert` off-screen slides, scroll-snap swipe.

### 2026-10-03 · A custom cursor vanished on accent surfaces (seed)
- Symptom: a custom cursor became invisible over a lime section.
- Cause: it was one fixed colour tested on dark and light grounds only.
- Rule: MOT-7. Keep it visible on every surface (blend mode or state-aware colour) and check each surface colour.

### 2026-10-03 · A mask image moved `load` and made a test measure the wrong moment (seed)
- Symptom: a test that read a style "just after load" passed on one page and failed on another by a few hundred milliseconds.
- Cause: a CSS mask image referenced from a stylesheet is a network request that delays `load`, and the page was animating; the test measured one moment of a moving state.
- Rule: PER-9 and TST-6. Read styles at `load`, poll for the settled value, never assume a fixed sleep covers a stagger.

### 2026-10-03 · Centred but not looking centred (seed)
- Symptom: a letter in a round badge looked shifted although its box was exactly centred.
- Cause: side bearings and ascender space make advance width differ from ink.
- Rule: LAY-13. Measure the glyph ink (canvas `actualBoundingBox*` or a pixel scan) and add an optical-offset token.

### 2026-10-03 · Every guard passed on a dead image (seed)
- Symptom: a hero image never rendered for any visitor, yet the HTML checks, the identity guard and an automated audit (best-practices 100) were all green.
- Cause: the page hot-linked images from an access-controlled origin that answers 403 with a JSON body; all guards read HTML as text and none fetched anything. An exemption granted to that origin had settled identity, and downstream readers took it as having settled delivery.
- Rule: PER-6 and TST-5. Copy media into the build; when the claim is that a page works, load it and read the network log. When you grant an exemption, say what it does NOT cover.

### 2026-10-03 · Hours lost to a failure that was already there (seed)
- Symptom: a failing test was investigated against the new diff for a long time.
- Cause: the test already failed on the committed code.
- Rule: TST-9. Run a failing test on the clean committed code before blaming your change. Also TST-8: a failing `beforeAll` shows as "skipped", so read the failed-files line.

### 2026-10-03 · Deploy surprises on a shared host (seed)
- Symptom: three separate incidents: a whole-root archive deploy was about to delete sub-sites and an app sharing the root; attaching a domain to a new site repointed its DNS within seconds; a CDN returned 403 to a headless browser while serving real browsers and crawlers.
- Cause: deploy style, host side effects and CDN bot rules were not checked before acting.
- Rule: PER-10, TST-10 and `references/caching-and-deploys.md`. List the root first, stage before attaching a live domain and read DNS records straight after, verify crawler access with `curl` and a crawler user agent, and drive live forms with requests.

### 2026-10-03 · A consent banner that stole focus (review)
- Symptom: on a first visit the banner script moved keyboard focus to "Accept all" on page load, while the banner itself was the last element in the body.
- Cause: focus was used to make up for a DOM position that keyboard users would otherwise reach last.
- Rule: A11Y. A non-modal banner never takes focus on load. Put it first in the body so Tab reaches it first; give Accept and Reject equal visual weight; start every optional toggle off; test that `document.activeElement` is the body after load and that the first Tab lands on the banner.

### 2026-10-03 · One extra space broke a byte-identical baseline (review)
- Symptom: a page for a site with the new feature turned off differed from its baseline by a single space.
- Cause: a new conditional block that renders nothing still sat between two blank-line-separated template expressions, and each whitespace run between expressions emits a space.
- Rule: TST. When output must stay byte-identical for sites without a feature, keep the new conditional adjacent to a neighbouring expression (or inside it), and run the byte-identical test on the merged code before trusting an implementer's claim about whose change caused a diff.

## Retired or corrected rules

None yet.
