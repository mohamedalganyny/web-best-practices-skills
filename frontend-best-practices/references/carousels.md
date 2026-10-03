# Accessible carousels

Supports CMP-7, MOT-4, MOT-5. First question: do you need a carousel? Stacked cards, a grid or a plain horizontal list with scroll-snap usually serve visitors better and cost nothing. If the design insists, build it this way. The pattern follows the W3C ARIA Authoring Practices carousel pattern (see `sources.md`, F2).

## Requirements, in plain words

1. If the carousel moves by itself, a pause/rotate button exists and is the FIRST stop in the tab order inside the carousel. WCAG 2.2.2 requires a way to pause anything that moves automatically for more than five seconds.
2. Automatic rotation stops when any element inside receives keyboard focus, and does not restart until the user asks.
3. Rotation also pauses while the pointer hovers, while the tab is hidden and while the carousel is off-screen.
4. Under `prefers-reduced-motion: reduce` there is no autoplay at all (and no rotate button, since nothing rotates).
5. Any user interaction (pointer down, keyboard, a click on prev/next) stops autoplay for good.
6. Slides that are not visible are `inert`, so keyboard and screen-reader users never land on something off-screen.
7. The slide container has `aria-live="off"` while rotating and `"polite"` otherwise.
8. Each slide has an accessible name ("3 of 10" is fine when nothing better exists); the control has a label that changes with the action ("Stop slide rotation" / "Start slide rotation").
9. Swipe works by native scrolling with scroll-snap. Do not write drag handlers.
10. Prev/next buttons are real buttons with 44px targets and mirrored arrows in RTL.

## Markup

```html
<section class="carousel" aria-roledescription="carousel" aria-label="Featured work">
  <div class="carousel__controls">
    <button type="button" class="carousel__rotate" hidden aria-label="Stop slide rotation">
      <svg aria-hidden="true" viewBox="0 0 24 24" width="20" height="20"><path d="M8 5h3v14H8zm5 0h3v14h-3z"/></svg>
    </button>
    <button type="button" class="carousel__prev" aria-label="Previous slide">...</button>
    <button type="button" class="carousel__next" aria-label="Next slide">...</button>
  </div>
  <div class="carousel__track" aria-live="off">
    <div class="carousel__slide" role="group" aria-roledescription="slide" aria-label="1 of 5">...</div>
    <div class="carousel__slide" role="group" aria-roledescription="slide" aria-label="2 of 5">...</div>
  </div>
</section>
```

Without JavaScript this is a scrollable, snap-aligned list with every slide reachable: progressive enhancement, not a requirement for content to show. The rotate button is `hidden` in the HTML; the script reveals it only when it starts autoplay (and the `sync` function keeps it hidden under reduced motion).

## CSS

```css
.carousel__track {
  display: flex;
  gap: var(--gap, 16px);
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  overscroll-behavior-x: contain;
  scrollbar-width: none;
  padding-inline: var(--gutter);
  scroll-padding-inline: var(--gutter);
}
.carousel__slide { flex: 0 0 min(100%, 28rem); scroll-snap-align: start; }
.carousel__prev svg, .carousel__next svg { transform: scaleX(var(--dir, 1)); }   /* mirror in RTL */
@media (prefers-reduced-motion: reduce) { .carousel__track { scroll-behavior: auto; } }
```

`display: flex` follows the document direction, so the first slide sits at the reading start in both LTR and RTL and scroll-snap needs no extra rule. Do not set `scrollLeft` by hand: its sign differs in RTL. Use `scrollIntoView({ inline: 'start', block: 'nearest' })`, which is direction-agnostic.

## Controller sketch

```js
export function initCarousel(root, { autoplayMs = 6000 } = {}) {
  const track = root.querySelector('.carousel__track');
  const slides = [...root.querySelectorAll('.carousel__slide')];
  const rotate = root.querySelector('.carousel__rotate');
  const reduced = matchMedia('(prefers-reduced-motion: reduce)');
  const holds = new Set();            // transient reasons not to rotate: 'hover', 'hidden', 'offscreen'
  let userStopped = false;            // sticky: only the rotate button clears it
  let timer = null;
  let index = 0;

  const rotating = () => !reduced.matches && !userStopped && holds.size === 0;

  const go = (i, smooth = true) => {
    index = (i + slides.length) % slides.length;
    slides[index].scrollIntoView({
      inline: 'start', block: 'nearest',
      behavior: smooth && !reduced.matches ? 'smooth' : 'auto',
    });
  };

  const sync = () => {
    clearInterval(timer);
    if (rotating()) timer = setInterval(() => go(index + 1), autoplayMs);
    track.setAttribute('aria-live', rotating() ? 'off' : 'polite');
    rotate.hidden = reduced.matches;
    rotate.setAttribute('aria-label', userStopped ? 'Start slide rotation' : 'Stop slide rotation');
  };

  const stopForGood = (e) => {
    if (e.target.closest('.carousel__rotate')) return;     // the button decides for itself
    userStopped = true;
    sync();
  };

  rotate.addEventListener('click', () => { userStopped = !userStopped; sync(); });
  root.querySelector('.carousel__prev').addEventListener('click', () => { userStopped = true; go(index - 1); sync(); });
  root.querySelector('.carousel__next').addEventListener('click', () => { userStopped = true; go(index + 1); sync(); });

  root.addEventListener('focusin', (e) => {                 // KEYBOARD focus anywhere inside, the control included, stops rotation
    if (e.target.matches(':focus-visible')) { userStopped = true; sync(); }
  });
  root.addEventListener('pointerdown', stopForGood);
  root.addEventListener('keydown', stopForGood);
  root.addEventListener('pointerenter', () => { holds.add('hover'); sync(); });
  root.addEventListener('pointerleave', () => { holds.delete('hover'); sync(); });
  document.addEventListener('visibilitychange', () => { document.hidden ? holds.add('hidden') : holds.delete('hidden'); sync(); });
  reduced.addEventListener('change', sync);

  new IntersectionObserver(([entry]) => {                     // page-level: off-screen carousels do not rotate
    entry.isIntersecting ? holds.delete('offscreen') : holds.add('offscreen');
    sync();
  }).observe(root);

  const slideObserver = new IntersectionObserver((entries) => {   // slide-level: off-screen slides are inert
    for (const e of entries) {
      e.target.inert = !e.isIntersecting;
      if (e.intersectionRatio >= 0.6) index = slides.indexOf(e.target);
    }
  }, { root: track, threshold: [0.01, 0.6] });
  slides.forEach((slide) => slideObserver.observe(slide));

  sync();
}
```

It is a sketch: adapt it, but keep every behaviour in the numbered list above.

Details worth keeping:

- **Why `:focus-visible` in the focus handler.** A mouse click on a button also fires `focusin`; without the check, clicking "Stop" would stop, then the click handler would toggle it back on.
- **Why `userStopped` and `holds` are separate.** Hover, tab-hidden and off-screen are temporary and clear themselves; a focus or a click is the user's decision and stays until they press the control.
- **Why `inert` and not `aria-hidden`.** `aria-hidden` leaves focusable children reachable by keyboard (an invisible tab stop). `inert` removes focus, pointer and assistive-technology access together.
- **Multi-card views.** `inert` applies only to cards with no intersection at all, so partly visible neighbours stay usable.
- **Looping.** Wrapping from the last slide to the first jumps a long distance; use `behavior: 'auto'` for the wrap or disable the buttons at the ends.

## Tests to write

1. Reduced motion emulated: after `autoplayMs * 2`, the scroll position has not changed and the rotate button is hidden.
2. Default motion: the first element in the tab order inside the carousel is the rotate button.
3. Tab into the carousel: rotation stops, the button's label says "Start slide rotation", and `aria-live` is `polite`.
4. Hover then leave without any other interaction: rotation resumes.
5. Click the rotate button twice: stopped, then rotating again (this catches the focus-then-click toggle bug).
6. Every slide that is not visible has `inert`; the visible one does not.
7. RTL: the first slide is at the right edge; "next" moves toward the left; the arrow icons point the reading direction.
8. Hidden tab (`document.hidden` forced) pauses rotation.

Break each behaviour once on purpose (remove the `inert` toggle, remove the `:focus-visible` check, remove the reduced-motion guard) and confirm its test goes red.
