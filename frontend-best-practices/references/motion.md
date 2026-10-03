# Motion, scroll behaviour and reduced motion

Supports MOT-1 to MOT-8 and CMP-3, CMP-4. The law behind all of it: motion decorates, it never carries information, it never delays the first meaningful paint, and it disappears for anyone who asks.

## The motion budget

- Animate only `transform` and `opacity` (`clip-path` and `filter` sparingly, on small elements).
- The LCP element (usually the hero image or heading) is never animated in and never starts invisible.
- Nothing animates before the LCP element has painted.
- No autoplay video above the fold.
- Medical, legal, price or contact information never depends on an animation having run.
- One signature moment per page type; everything else is restrained.

## The reveal pattern

CSS:

```css
.reveal { transition: opacity 0.6s ease, transform 0.6s ease; }   /* transition on the BASE element */

.reveal-ready .reveal:not(.is-visible) {
  opacity: 0;
  transform: translateY(16px);
}

@media (prefers-reduced-motion: reduce) {
  .reveal-ready .reveal:not(.is-visible) { opacity: 1; transform: none; }
  .reveal { transition: none; }
}
```

JavaScript (inline or deferred, tiny):

```js
(() => {
  if (matchMedia('(prefers-reduced-motion: reduce)').matches || !('IntersectionObserver' in window)) return;
  const items = [...document.querySelectorAll('.reveal')];
  const cutoff = innerHeight;                       // anything inside the first viewport counts as "already in view"

  // 1. Whatever is already in view never animates and never hides.
  const below = [];
  for (const el of items) {
    if (el.getBoundingClientRect().top < cutoff) el.classList.add('is-visible');
    else below.push(el);
  }

  // 2. Only now may anything wait in the hidden state.
  document.documentElement.classList.add('reveal-ready');

  // 3. Let the hidden state paint, THEN start observing, or the reveal is skipped.
  const io = new IntersectionObserver((entries) => {
    for (const e of entries) {
      if (!e.isIntersecting) continue;
      e.target.classList.add('is-visible');
      io.unobserve(e.target);
    }
  }, { threshold: 0, rootMargin: '0px 0px -10% 0px' });

  requestAnimationFrame(() => requestAnimationFrame(() => below.forEach((el) => io.observe(el))));
})();
```

Why each decision:

- No JavaScript, a script error or reduced motion leaves `.reveal-ready` unset, so the content is simply visible.
- `threshold: 0` with a bottom `rootMargin` fires when the element's top crosses roughly the 90% line. A `threshold` of 0.5 never fires for elements taller than twice the viewport.
- The double `requestAnimationFrame` guarantees one painted frame in the hidden state. Without it the browser batches "hidden" and "visible" into one style recalculation and the transition never runs: the reveal is wired but invisible.
- Elements that start in view are excluded: animating things the visitor is already looking at is noise, and it can move the LCP. Using the full viewport height as the cutoff (not the 90% line of the observer) also stops a sliver at the bottom edge from fading out when the ready class is added.
- Stagger with `transition-delay` set from a custom property (`style="--i: 3"`), capped at about 400 ms in total.

## Scroll-driven CSS

```css
.card { opacity: 1; }                                  /* the finished state is the default */

@supports (animation-timeline: view()) {
  @media (prefers-reduced-motion: no-preference) {
    .card {
      animation: rise linear both;                     /* shorthand first: it resets animation-timeline */
      animation-timeline: view();
      animation-range: entry 0% entry 60%;
    }
    @keyframes rise {
      from { opacity: 0; transform: translateY(24px); }
      to   { opacity: 1; transform: none; }
    }
  }
}
```

Support was not Baseline at the last check (see `sources.md`), so treat it as an enhancement only. Never rely on it for something the page needs.

## Hide-on-scroll header

Rules: hide only on a downward scroll past about two header heights; show immediately on any upward scroll; always visible near the top, while the menu is open and while KEYBOARD focus is inside; transform only; never under reduced motion.

```css
.site-header { position: sticky; top: 0; transition: transform 0.3s ease; }
.site-header[data-hidden="true"] { transform: translateY(-100%); }
```

```js
(() => {
  if (matchMedia('(prefers-reduced-motion: reduce)').matches) return;
  const header = document.querySelector('.site-header');
  const JITTER = 8;
  let lastY = scrollY;
  let queued = false;

  const pinned = (y) =>
    y < header.offsetHeight * 2 ||
    header.querySelector('[aria-expanded="true"]') !== null ||   // menu open
    header.querySelector(':focus-visible') !== null;             // keyboard focus inside

  const update = () => {
    queued = false;
    const y = Math.max(0, scrollY);
    const delta = y - lastY;
    if (Math.abs(delta) < JITTER) return;                        // slow scrolls accumulate: lastY is not advanced
    lastY = y;
    header.dataset.hidden = String(!pinned(y) && delta > 0);
  };

  addEventListener('scroll', () => {
    if (!queued) { queued = true; requestAnimationFrame(update); }
  }, { passive: true });
})();
```

Notes:

- Use `:focus-visible`, not `:focus-within`. After a tap on the menu toggle the focus stays on it; with `:focus-within` the header would then be pinned forever.
- A hidden header still holds focusable controls. When a keyboard user tabs into it, `:focus-visible` is true and the next scroll shows it; also call `header.dataset.hidden = 'false'` on `focusin` from the keyboard.
- Listen on the element that actually scrolls. If the page scrolls inside a wrapper (a scroll container for snap, say), `window` never fires.
- Do not read layout inside the handler except the header height; cache it and refresh it with a `ResizeObserver`.
- `html { scroll-padding-top: var(--header-h); }` keeps anchors and focused elements out from under the header.

## Reduced motion

- Under `prefers-reduced-motion: reduce`: no parallax, no marquee or ticker motion, no smooth-scroll hijacking, no carousel autoplay, no header hiding, no looping video. A short opacity fade is acceptable.
- Write motion as an opt-in (`no-preference`) or cancel it under `reduce`; do not rely on a global `* { animation: none }` that also kills essential feedback such as a loading indicator.
- Re-test with the OS setting, not only emulation, once before launch.

## Marquees and tickers

Bleed the full width, loop a doubled track, pause on hover and focus-within, offer a pause control when motion lasts more than five seconds, stay static under reduced motion. Direction in RTL is covered in `rtl-and-bilingual.md`.

## Custom cursors and magnetic effects

- Mount only under `(hover: hover) and (pointer: fine)`.
- Never hide the system cursor; the custom element follows it.
- Never put information in the cursor.
- Keep it visible on every surface. A dark dot disappears on a dark section and an accent-coloured ring disappears on an accent flood; use a blend mode or switch colour by the surface under it, and check each surface colour with a screenshot.
- Disable it while a menu or dialog is open and for touch.

## Preloaders

Avoid them on marketing pages: they delay the content the visitor came for. If a design insists, the real content is fully rendered underneath, the loader never delays the LCP element, it is skipped under reduced motion and for return visits, and it lasts no longer than the assets it waits for.

## Tests that prove motion

```js
// 1. The reveal is SEEN: sample opacity while scrolling.
await page.emulateMedia({ reducedMotion: 'no-preference' });
await page.goto(url, { waitUntil: 'load' });
const target = page.locator('.reveal').nth(5);
await page.evaluate(() => window.scrollBy({ top: 700 }));
const samples = [];
for (let i = 0; i < 20; i++) {
  samples.push(await target.evaluate((el) => Number(getComputedStyle(el).opacity)));
  await page.waitForTimeout(40);                       // sampling interval, not a settle guess
}
expect(samples.some((v) => v > 0 && v < 1)).toBe(true);   // caught mid-reveal
expect(samples.at(-1)).toBe(1);                           // and it finished

// 2. Reduced motion: everything is visible immediately.
await page.emulateMedia({ reducedMotion: 'reduce' });
await page.reload({ waitUntil: 'load' });
expect(await page.locator('.reveal').evaluateAll((els) =>
  els.every((el) => getComputedStyle(el).opacity === '1'))).toBe(true);

// 3. No JavaScript: everything is visible.
const ctx = await browser.newContext({ javaScriptEnabled: false });
```

Break each mechanism once: remove the double `requestAnimationFrame` (test 1 must fail), set the hidden state without the ready class (test 3 must fail), delete the reduced-motion block (test 2 must fail).
