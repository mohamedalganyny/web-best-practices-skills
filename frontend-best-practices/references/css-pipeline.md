# CSS pipeline pitfalls

Supports CSS-1 to CSS-9. Everything here is a way for CSS or markup to be correct in the editor and wrong in the shipped page. The common cure: inspect the BUILT output, not the dev server.

## 1. Where does the override land?

Frameworks that scope component styles often append the component stylesheet at the end of `<head>`. A `<link rel="stylesheet">` you place in the layout's `<head>` therefore comes BEFORE it and loses every tie at equal specificity.

- Symptom: your override is "applied" in the source and the hero is 90px short in the browser.
- Fix: raise specificity deliberately (prefix the selector with a page-level root class), or inject the override last (a build step, or an `@layer` order declared up front: `@layer base, components, overrides;` with the override in the highest layer).
- Test: parse the built HTML and assert the override `<link>` index is after the framework's; compare screenshots at the widest size.

## 2. Scoped styles and the global escape

Scoped-style compilers attach their own attribute to every compound selector. A rule such as `[dir="rtl"] svg { transform: scaleX(-1) }` compiles to `[data-x][dir="rtl"] svg[data-x]` and matches nothing, because the ancestor does not carry the component's attribute.

Needs the global escape (`:global(...)` or the framework's equivalent):

- any ancestor the component did not render itself (`<html dir="rtl">` from the layout);
- a child component's root element;
- slotted or injected content.

There is no error; the dev server may even render it correctly. Check the built page, in RTL, on every screen.

## 3. Global styles ship everywhere

A component's global style (`is:global`, a global stylesheet imported by the component) is included on EVERY page that bundles it, and in an engine that imports every section component, that is every page of every site.

- Scope each rule to the component root: `:where(.hero) .hero__cta { ... }`. `:where()` adds no specificity, so theme hooks elsewhere keep winning.
- Never ship bare `.title`, `.cta`, `.subheading` rules.
- Proof is a measurement: render an UNTOUCHED, older component in a browser and compare its box sizes before and after your change. Text-based tests stay green while the layout breaks.
- A dynamically imported component's scoped CSS can still be bundled into every page head; do not rely on lazy import to isolate styles.

## 4. The class scanner reads everything

Utility frameworks such as Tailwind v4 scan source files as plain text (`.gitignore`d files, `node_modules`, binaries, CSS and lock files excepted). Any token that looks like a class becomes CSS:

- text inside test fixtures and snapshots;
- documentation, comments, release notes;
- ordinary words in a component's own CSS (`ease-out` in a `transition` declaration; an event name such as `hover`).

Symptoms: dead utilities and theme variables in every tenant's production CSS, bundles that grow after "adding a test".

Fix and guard:

- Exclude folders: `@source not "../../tests";` (and docs, fixtures, snapshots).
- Add a build assertion that greps the produced CSS for a deny-list (variables or utilities you never wrote).
- When you rename a token, search tests and docs too; they are inputs.

## 5. No file-system reads at render time

A component that does `readFileSync(new URL('./x.css', import.meta.url))` works in dev and in a component-rendering test, and throws `ENOENT` in the production build because the bundler moved the component's code and never copied the file.

- Import the asset through the bundler (`import css from './x.css?inline'`).
- Or put the shared value in a plain TypeScript or JSON constant.
- A render-time `fetch` of your own CMS is the network version of the same mistake (PER-6).

## 6. Comments in templates

Template compilers can emit HTML comments verbatim. An internal note such as "TODO: ask the owner" or a path then ships to every visitor and to search engines.

- Keep notes in the template's code fence or language-level comments.
- Add a build check: fail on `<!--` in built HTML (conditional comments are dead), and on internal phrases (local paths, tool names, project code names).

## 7. The shared engine never knows a client

In a multi-site engine:

- No `if (slug === 'x')` branches in shared code. A guard test greps shared folders for slug comparisons and fails.
- Differences are settings, declared overrides in a per-site folder, or variants.
- A variant is a handful of bounded tokens in one `:root` block; a guard reads the whole file. It is never a new component.
- A per-site override adds; it never patches a shared component. More than about three overrides on one site means the engine is missing a variant.

## 8. Defaults that surprise

- Browsers italicise `var`, `em`, `i`, `cite`, `dfn`, `address`. Reset them wherever the design has no italics: count-up numbers inside `<var>` rendered slanted.
- `tabular-nums` on counters and `white-space: nowrap` on number plus suffix.
- `font-synthesis: none`, so a missing weight is visibly missing instead of faked.
- Vendor- or tool-named default output paths and attributes can leak tooling names into public pages; rename the asset directory if the identity rules of the project forbid it.

## 9. Tokens first

- Define colour, type scale, spacing, radii, container widths, easings and durations before writing components (a design-tokens file or one `:root` block).
- Components consume tokens only; a literal colour in a component is a review comment.
- Read the tokens back from the design's computed styles (see `verification.md`), then they cannot drift.

## 10. Quick audit list for a built site

- [ ] Override stylesheet comes after the framework CSS.
- [ ] Rules touching ancestors, child roots or slots use the global escape and work in RTL.
- [ ] Every global rule is scoped to a component root.
- [ ] Built CSS contains no utility or variable you never wrote.
- [ ] No `readFileSync` (or equivalent) in components.
- [ ] No `<!--` and no internal phrases in built HTML.
- [ ] No tenant slug comparisons in shared code.
