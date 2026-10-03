---
name: seo-best-practices
description: Use when planning, building, auditing or publishing any public website or page that should be found in search engines or AI assistants - URLs, titles, headings, locale routing, sitemaps, structured data, content plans, local search, links, consent and tracking tags, and AI-search readiness. Every item carries the date its source was checked. It updates itself - it re-checks fast-moving guidance when stale and logs new lessons after every use. Pair it with frontend-best-practices when you also build the pages.
last_verified: 2026-10-03
related: frontend-best-practices
---

# SEO best practices

`last_verified: 2026-10-03` - the date every `[fast]` item below was last re-checked against its source (see `references/sources.md`).

A working checklist for search and AI-search visibility, vetted against primary documentation instead of folklore. Items tagged `[S#, date]` point to `references/sources.md`; `[C, 2026-10-03]` marks a convention or house rule with no primary source; `[fast]` marks guidance that changes often and must be re-checked when this skill is stale.

## When to call this skill

Call it:

- when you plan a new site: URL scheme, locales and which one is primary, page types, content plan, tracking and consent;
- when you write or review templates that emit titles, headings, canonicals, hreflang, sitemaps, robots.txt or structured data;
- when you write or edit content meant to rank or be cited: briefs, outlines, articles, service pages, local pages;
- when you migrate, rename routes, redesign or deploy a site (redirects and stale pages are an SEO event);
- when you add analytics, ad pixels or a consent banner;
- before launch, and during monthly or quarterly audits.

Do not call it for internal tools, authenticated apps or pages that must not be indexed (use only the technical items on `noindex`).

Call `frontend-best-practices` as well whenever you also build the pages: it owns Core Web Vitals implementation, RTL layouts, motion, caching and testing. This skill owns what search engines and AI assistants need from the pages.

## Self-update protocol (follow it every time you use this skill)

1. **Check freshness.** Read `last_verified` above. If it is older than 30 days (or missing), re-check every `[fast]` item - search-engine guidance, supported structured-data features, AI crawler names and behaviour, consent and tag requirements - with a web search or by fetching the source in `references/sources.md`. Update the item, its date in `sources.md` and the inline date, then set `last_verified` to today only when all `[fast]` items were re-checked. If you cannot reach the web, say so in your answer, change no dates and treat `[fast]` items as unconfirmed.
2. **Read `LESSONS.md` before you start** and apply every lesson newer than your last use. Whatever the date, search for the current wording of any specific claim you are about to rely on in a client-facing deliverable.
3. **After the work, append new lessons to `LESSONS.md`**: date, symptom, cause, rule. Add only lessons that generalise - ask whether a stranger on a different project would hit the same thing. If a lesson changes a rule, edit the rule here, keep the old wording in the lesson ("Replaces: ...") and add a line to `CHANGELOG.md`.
4. **Save a one-line pointer to the agent's memory** (if it has persistent memory): the skill name, where it lives and when to call it. Example: "Skill `seo-best-practices` is installed; call it when planning, building, auditing or publishing a website; companion `frontend-best-practices`." Update the pointer rather than adding a second.
5. **Never store secrets, client names, private URLs, internal paths or personal data** in this skill, its lessons or its memory pointer. Describe situations neutrally ("a clinic site in Arabic and English").

## Before you start

- [ ] Run the self-update protocol (steps 1 and 2).
- [ ] Write down per site: the primary locale (any locale can be primary), the other locales, the canonical host (one of apex or `www`), and the owner's decision on AI training crawlers.
- [ ] Name the page types and the one intent each answers; map them on the funnel (CONT-1).
- [ ] Find the single source of truth for entity facts (name, address, phone, people, services, prices) that templates and structured data will read.
- [ ] List every tracking tool and ad pixel that will run, and the consent behaviour for each (TAG group).
- [ ] If this is a migration, collect the old URLs and sitemap first (TECH-14).
- [ ] If you also build the pages, open `frontend-best-practices` now.

## 1. Technical

- **TECH-1 `[fast]` Indexable, static HTML with snippets allowed is the whole eligibility for AI Overviews and AI Mode.** Put the real content in the HTML (not only injected by script), and do not block snippets on pages you want shown. `[S1, S2 - 2026-10-03]`
- **TECH-2 HTTPS everywhere, one canonical host, mobile-first.** The mobile page carries the same content as the desktop page. `[C, 2026-10-03]`
- **TECH-3 `[fast]` Core Web Vitals at the 75th percentile: LCP up to 2.5 s, INP up to 200 ms, CLS up to 0.1.** INP replaced FID in 2024. Implementation lives in `frontend-best-practices`. `[S22, S23 - 2026-10-03]`
- **TECH-4 Self-referencing canonical on every page of every locale,** absolute URLs, one per page. It is a strong signal, not a command. `[S7 - 2026-10-03]`
- **TECH-5 robots.txt controls crawling, not indexing.** To keep a page out of search use `noindex` and leave the page crawlable; if robots.txt blocks it, the crawler never sees the `noindex`. `[S6, S34 - 2026-10-03]`
- **TECH-6 `[fast]` Sitemaps:** one index with a sitemap per locale, listing canonical indexable URLs only; `lastmod` is the content's real change time (never the build time) because Google uses it only if it is consistently and verifiably accurate; `priority` and `changefreq` are ignored. Reference the index in robots.txt and submit it to Google and Bing. `[S4 - 2026-10-03]`
- **TECH-7 Ping IndexNow after each deploy** for the engines that use it (Bing, Yandex, Naver, Seznam, Yep, Amazon). Google does not participate. `[S24 - 2026-10-03]`
- **TECH-8 URLs are short, readable, stable and hyphenated.** Non-ASCII slugs (Arabic, for instance) are fine, but Google recommends percent-encoding non-ASCII characters in the URLs you emit; apply one form everywhere: links, canonicals, sitemaps, hreflang. `[S30 - 2026-10-03]`
- **TECH-9 Titles and descriptions are written per page and locale.** There is no fixed title length: Google truncates to device width and may rewrite titles. A meta description is used for the snippet when it describes the page better than the text does; it is not documented as a ranking input. `[S8, S9 - 2026-10-03]`
- **TECH-10 One `<h1>` and an ordered `<h2>`-`<h6>` outline** for readers and assistive technology. It is convention, not a ranking rule. `[C, 2026-10-03]`
- **TECH-11 Crawlable internal links:** real `<a href>` elements with descriptive anchor text; every page you care about is linked from at least one other page; no orphans. `[S16 - 2026-10-03]`
- **TECH-12 Images:** original, with descriptive file names and localised `alt` text (no keyword stuffing); supported formats include WebP, AVIF and SVG. EXIF or geotag metadata is not an image-SEO factor. `[S35 - 2026-10-03]`
- **TECH-13 Run a broken-link and crawl check in every build** (internal 200s, no redirect chains, no orphans, hreflang return links), and a full crawl audit at launch and quarterly. `[C, 2026-10-03]`
- **TECH-14 Redirects survive migrations.** Every old URL gets a permanent redirect to its equivalent before launch, compiled from data, kept as long as possible (at least a year). Test each one live, per locale, with a made-up child path too: some server directives are prefix matches. Redirect data entered by editors may only point at the site's own host over https: drop off-site targets, loops, cycles and query-string sources at build time and log each drop. `[S38 - 2026-10-03]`
- **TECH-15 Renames need redirect AND removal.** A deploy that never deletes leaves every old page live as duplicate content with a self-canonical. Overwrite stale files with an instant-refresh stub carrying `rel="canonical"` to the new URL (no `noindex`: it contradicts the canonical). See `LESSONS.md`. `[C, 2026-10-03]`
- **TECH-16 Verify with the property tools:** Search Console (Page indexing, Crawl stats, URL Inspection) and Bing Webmaster Tools, verified for every property. The old "Crawl errors" report is gone. `[S32 - 2026-10-03]`
- **TECH-17 Do not host third-party content on a client's domain to borrow its ranking** (site reputation abuse). `[S10 - 2026-10-03]`
- **TECH-18 Check the live site the way crawlers see it:** `curl` with the crawler's user agent, expecting 200 and real HTML. A CDN may challenge headless browsers and still serve crawlers. `[C, 2026-10-03]`
- **TECH-19 Stage before you attach a live domain to new hosting.** Attaching a domain can repoint its DNS within seconds; read the DNS records straight after any host operation and never touch mail records by accident. See `frontend-best-practices/references/caching-and-deploys.md`. `[C, 2026-10-03]`

## 2. International and bilingual

- **INTL-1 Any locale can be primary; never assume it.** Routes, `dir`, fonts, hreflang, sitemaps and `x-default` come from the site's configuration. `x-default` points at the primary-locale page. `[S5 - 2026-10-03]`
- **INTL-2 hreflang is reciprocal and self-referencing:** every language version lists itself and all others; without return links the annotations may be ignored. `[S5 - 2026-10-03]`
- **INTL-3 Never auto-redirect by browser language.** Offer a selector and rely on `x-default`. `[S5 - 2026-10-03]`
- **INTL-4 A missing translation falls back to the primary content by rewrite (a 200), not a redirect.** Decide per URL: either do not publish the locale URL, or serve the fallback with the canonical pointing at the primary version and leave that locale out of hreflang. `[C, 2026-10-03]`
- **INTL-5 Write each locale natively,** not as translationese; machine translation without added value is scaled content abuse. Localise titles, descriptions, `alt` text and structured data `inLanguage`. `[S10 - 2026-10-03]`

## 3. On-page

- **PAGE-1 One primary intent per page.** Say what the page is and who it serves in the opening lines, in the reader's words. Google documents no keyword-position rule; write for people. `[S14 - 2026-10-03]`
- **PAGE-2 Put a direct, summarisable answer in the opening paragraph** (definition, price range, who it is for, next step). It serves readers, featured snippets and AI assistants alike. `[C, 2026-10-03]`
- **PAGE-3 Keep visible FAQs where the questions are real.** FAQ markup is optional entity data now (SCHEMA-3). `[S3 - 2026-10-03]`
- **PAGE-4 Every call to action works and matches the promise** of the ad or snippet that led there. Search experience optimisation = fast pages, clear next step. `[C, 2026-10-03]`
- **PAGE-5 Video:** a dedicated watch page per video where it makes business sense, embedded with a crawlable `<video>` or `<iframe>`, plus `VideoObject` consistent with the video. A short video per pillar page is a good default. `[S15 - 2026-10-03]`
- **PAGE-6 Internal linking is hub-and-spoke:** each cluster links up to its pillar and across to siblings with descriptive anchors. `[C, 2026-10-03]`
- **PAGE-7 Dates mean something.** Show a visible published and updated date; change the updated date (and `dateModified`) only after a substantive edit, and keep visible and structured dates consistent. `[S36 - 2026-10-03]`
- **PAGE-8 Run a readability pass in every language,** and write articles from an outline first. `[C, 2026-10-03]`

## 4. Content

- **CONT-1 Funnel order:** bottom-of-funnel first (service pages, pricing guides, ROI calculators, demo and booking pages), then middle (case studies, comparisons, alternatives, implementation guides), then top (how-to, definitions, trends, interviews). `[C, 2026-10-03]`
- **CONT-2 Clusters, not solo posts.** Every post belongs to a cluster and links to its pillar; make the cluster a required field. `[C, 2026-10-03]`
- **CONT-3 Publishing cadence is a business choice, not a ranking rule.** AI-assisted content is allowed; many pages without added value is scaled content abuse. `[S10 - 2026-10-03]`
- **CONT-4 Refresh at 60-90 days when something substantive changed;** a date bump alone is not a refresh. `[S36 - 2026-10-03]`
- **CONT-5 Real authors, credentials and bylines that link to a biography;** firsthand experience over generic advice. E-E-A-T is a quality framework, not a ranking factor; trust matters most. Health, finance, legal and safety topics (YMYL) need a qualified, named reviewer. `[S14 - 2026-10-03]`
- **CONT-6 Nothing an automated writer produces auto-publishes.** Every agent change is a draft with the agent as author in the version history; canonical, `noindex` and redirects change only on a human click. `[C, 2026-10-03]`
- **CONT-7 State the business identity** (legal name, registered address, tax or registration number) on terms and privacy pages and in `Organization` markup; ad platforms look for it. `[S13 - 2026-10-03]`
- **CONT-8 Articles that make legal or regulatory claims** (for example what advertising may say) are researched from primary sources first, the sources are kept, and the page says it is not legal advice. `[C, 2026-10-03]`
- **CONT-9 Regulated advertising:** follow the local norms for the sector, substantiate every claim, and publish before/after or patient imagery only with documented consent on file (unconsented items are filtered out of the build regardless). `[C, 2026-10-03]`

## 5. Structured data

- **SCHEMA-1 Generate JSON-LD from records, never type it by hand,** type it with `schema-dts`, and run a rich-results or schema validation in CI. `[C, 2026-10-03]`
- **SCHEMA-2 Mark up only what is visible and true on the page.** Hidden or misleading markup risks a manual action. `[S12 - 2026-10-03]`
- **SCHEMA-3 `[fast]` Use live types.** The search gallery lists Article, Breadcrumb, Carousel, Event, Image metadata, Local business, Organization, Product, Profile page, Review snippet, Video and others; FAQ and How-to are absent. The FAQ rich result stopped showing on 2026-05-07 (documentation removed 2026-06-15); How-to ended in 2023. `FAQPage` stays only as harmless entity data, or drop it. `[S3, S29 - 2026-10-03]`
- **SCHEMA-4 `Service`, `Offer`, `Physician` and similar types give no Google rich result** but are useful, consistent entity data. Keep them factual and equal to the visible text. `[C, 2026-10-03]`
- **SCHEMA-5 `[fast]` No self-serving review stars.** `LocalBusiness` or `Organization` pages whose reviews the entity controls are ineligible for the star feature; fake or undisclosed-incentive reviews in markup are banned (guideline added 2026-07-24). `[S11, S3 - 2026-10-03]`
- **SCHEMA-6 `Organization` carries `name`, `legalName`, `address`, `taxID` where applicable, `logo` and `sameAs`** (profile pages on other sites), identical to the Business Profile and directory listings. `[S13 - 2026-10-03]`
- **SCHEMA-7 `[fast]` `VideoObject` accepts `creator` (added 2026-09-24).** `[S3 - 2026-10-03]`
- **SCHEMA-8 One source of truth per entity:** local business from locations, person from people records, article from posts, service and offer from services. NAP and prices in markup equal the visible page. `[C, 2026-10-03]`
- **SCHEMA-9 Prices:** the static HTML and the `Offer` carry one canonical currency; a script may swap the displayed currency for the visitor but must show the canonical equivalent next to it and leave markup and crawlable text unchanged. `[C, 2026-10-03]`

## 6. AI search (AIO, GEO, AEO)

- **AI-1 `[fast]` For Google, optimising for generative AI is still SEO.** A page needs to be indexed and snippet-eligible; no special schema, no AI text files, no Markdown, no chunking into tiny pieces. `[S1, S2 - 2026-10-03]`
- **AI-2 State the entity and the offer in the first 100 words** (who you are, what you do, for whom, where), and keep company facts identical across the sources models learn from: your site, business profile, directories, professional networks, press. `[C, 2026-10-03]`
- **AI-3 Write clear, factual, summarisable pages:** answer first, define terms, give numbers with units and dates, and claim only what you can substantiate. `[C, 2026-10-03]`
- **AI-4 `[fast]` Allow search and user-fetch agents and name each one explicitly in robots.txt:** OAI-SearchBot, ChatGPT-User, Claude-SearchBot, Claude-User, PerplexityBot, Googlebot (plus OAI-AdsBot if you run that platform's ads). Full table and template in `references/ai-crawlers.md`. `[S18, S19, S20 - 2026-10-03]`
- **AI-5 `[fast]` Training crawlers are the owner's decision, recorded per site:** GPTBot, ClaudeBot, Google-Extended, Applebot-Extended. `Google-Extended` does not affect Search inclusion or ranking; `Applebot-Extended` does not crawl at all. `[S17, S19, S21 - 2026-10-03]`
- **AI-6 robots.txt does not bind every agent.** User-initiated fetchers (ChatGPT-User, Perplexity-User) and OpenAI's ads checker may not follow it. Never use robots.txt as confidentiality. `[S18, S20 - 2026-10-03]`
- **AI-7 llms.txt is cheap and optional.** Google says it neither helps nor harms and does not use it; no major engine documents using it. Publish if you like; never rely on it. `[S1 - 2026-10-03]`
- **AI-8 Serve the same content to every agent.** Markdown twins or per-bot versions draw a cloaking concern. `[S41 - 2026-09-27, secondary]`
- **AI-9 The four questions for every page:** will it rank, will the model learn about me, will the model recommend me, will it turn traffic into action? `[C, 2026-10-03]`
- **AI-10 `[fast]` Agent readiness in tiers.** Tier 1, every site: explicit AI-bot robots.txt, sitemap index with honest `lastmod` and the robots line, IndexNow ping, all content in static HTML, optional llms.txt. Tier 2, only when the site has an API, booking or checkout: `/.well-known/api-catalog` (RFC 9727), OAuth metadata (RFC 9728, RFC 8414), an agent-skills index or MCP server card once they exist, a form-tool pilot. Tier 3, watch only: Markdown negotiation, new discovery or bot-authentication drafts, agent payment protocols. `[S37, S43 - 2026-10-03; S39, S44 checked earlier or unverified]`
- **AI-11 Ad platforms review the landing page:** ad copy matches the page, the form or booking works, the privacy policy and business identity are visible, claims are substantiated, and software pages describe software only. `[S45 - unverified]`

## 7. Local

- **LOC-1 Google Business Profile first.** Local ranking rests on relevance, distance and prominence; there is no way to request or pay for a better local rank. `[S25 - 2026-10-03]`
- **LOC-2 NAP (name, address, phone) from one structured source,** identical on the site, the profile, directories and markup. `[C, 2026-10-03]`
- **LOC-3 One page per REAL location** and per real service area with unique content. City-clone pages that funnel visitors to one destination are doorway abuse; neighbourhood pages need genuinely unique, useful content. `[S10 - 2026-10-03]`
- **LOC-4 Ask every customer for a review; never gate, filter or incentivise.** Discouraging negative reviews, selectively soliciting positive ones and paying for reviews are prohibited. Reply to reviews. `[S33 - 2026-10-03]`
- **LOC-5 Add a click-to-call, map and booking action** (a messaging button is a legitimate contact channel for a business's own customers). `[C, 2026-10-03]`

## 8. Off-page

- **OFF-1 Earn links through PR, original data, expert quotes, partnerships and relevant directories.** Track them in a register: target, anchor, type, date, status. `[C, 2026-10-03]`
- **OFF-2 Paid, sponsored or exchanged links carry `rel="sponsored"`; user-generated links carry `rel="ugc"`.** `[S10 - 2026-10-03]`
- **OFF-3 No routine disavow.** Use the disavow tool only for a manual action or a clear unnatural-link problem; a quarterly link review is for outreach quality, not for "toxic score" clean-ups. `[S31 - 2026-10-03]`
- **OFF-4 Monitor brand mentions** and turn unlinked mentions into citations; keep entity facts consistent. `[C, 2026-10-03]`
- **OFF-5 Journalist-request services are PR, not link building.** The original service shut down in December 2024 and was relaunched under another operator in April 2025. `[S42 - unverified]`

## 9. Privacy, consent and tags

- **TAG-1 Tracking IDs are typed fields, never pasted HTML or script.** A raw script field is stored XSS and forces `unsafe-inline` into the content-security policy; typed IDs let the build emit known loaders and a per-site allow-list. A tag-manager container means whoever holds it can run arbitrary code: offer it only when the client insists, prefer direct tags. `[C, 2026-10-03]`
- **TAG-2 `[fast]` Consent is denied by default, with one banner for every visitor.** Use Consent Mode v2 (`ad_storage`, `analytics_storage`, `ad_user_data`, `ad_personalization`): call `gtag('consent', 'default', ...)` before any measurement command and `update` on the visitor's choice. No vendor script loads before a click. `[S26 - 2026-10-03]`
- **TAG-3 Privacy law requires prior consent in many places,** so one banner is simpler than regional logic; the privacy and cookie policies must list exactly the tracking tools the site runs. `[C, 2026-10-03; regional details S40 - unverified]`
- **TAG-4 `[fast]` Some vendors enforce consent signals regionally** (one session-replay tool does for EEA, UK and Switzerland visitors since 2025-10-31). Check each vendor's current rule. `[S28 - 2026-10-03]`
- **TAG-5 Load after consent and after `load`:** an inline bootstrap holds the enabled IDs; a fixed-position banner causes no layout shift and is never the LCP element; on accept, load vendors after `load` plus `requestIdleCallback` (3 s timeout) or on first interaction. No `<noscript>` pixels (they fire without consent). See `references/consent-and-tags.md`. `[C, 2026-10-03]`
- **TAG-6 Attribution is captured only after consent:** UTM parameters, click ids (`gclid`, `gbraid`, `wbraid`, `fbclid`, `ttclid`, `msclkid`, `li_fat_id`, `ScCid`), landing URL and first-touch time. Build Meta's `_fbc` as `fb.1.<ms>.<fbclid>` with case preserved. `[C, 2026-10-03]`
- **TAG-7 `[fast]` One `event_id` per conversion, shared by the browser pixel and the server event,** with the same event name; send server events only when `ad_user_data` was granted. Meta de-duplicates matching pairs received within 48 hours. `[S27 - 2026-10-03]`
- **TAG-8 Verify tags in two ways:** the vendors' debug tools (tag assistant, event debug views, test-events screens, pixel helpers) and an automated test on a local build - banner rejected means zero vendor requests. `[C, 2026-10-03]`
- **TAG-9 Search-engine verification meta tags are static** and need no consent. `[C, 2026-10-03]`

## 10. Measurement

- **MEAS-1 Measure outcomes first:** leads, bookings and calls by page, Search Console clicks and impressions, Bing Webmaster data. Rank tracking and AI-prompt checks are noisy monitoring, not results. `[C, 2026-10-03]`
- **MEAS-2 Cadence:** weekly rank tracking per priority group, monthly top-5 competitor watch, a monthly report (rank shifts, link velocity, content shipped, leads by page), a quarterly technical audit, and a crawl audit at launch. `[C, 2026-10-03]`

## Myths to drop

Not ranking factors or not worth the effort, per the sources in `references/sources.md`: LSI keywords; word-count targets; the meta keywords tag; routine toxic-link clean-ups and disavow; geotagged or EXIF images; "near me" stuffed into copy; H1 order or keyword position as a ranking factor; E-E-A-T as a direct ranking factor; FAQ and How-to rich results; `priority` and `changefreq` in sitemaps; llms.txt or Markdown twins helping Google; chunking pages for AI; voice search as its own discipline; "predictive analytics SEO"; review gating or incentives; city-clone pages; date bumps passed off as refreshes.

## Before you publish

- [ ] Title and description unique per page and locale; one intent answered in the first lines.
- [ ] Canonical self-referencing; hreflang reciprocal with `x-default` = primary; no language auto-redirect.
- [ ] Sitemap index per locale with honest `lastmod`; listed in robots.txt; submitted; IndexNow pinged.
- [ ] robots.txt names the AI agents; no disallowed page that also needs a `noindex`.
- [ ] Structured data generated from records, visible content only, live types only, no self-serving stars; validated in CI.
- [ ] Redirects tested live per locale including a child path; old files removed or stubbed; CDN purged.
- [ ] Internal links crawlable and descriptive; no orphans; broken-link check green.
- [ ] Authors, reviewers (YMYL), business identity and dates are real and visible.
- [ ] Consent default denied; rejected banner means zero vendor requests; policies list the tools in use.
- [ ] Core Web Vitals checked on the built page (see `frontend-best-practices`); `curl` as a crawler returns real HTML.
- [ ] New lessons appended to `LESSONS.md`; memory pointer saved.

## Related skill: frontend-best-practices

Call `frontend-best-practices` when:

- an SEO requirement becomes a template: Core Web Vitals work (LCP image, CLS, fonts), static rendering, locale layouts and RTL, language switch;
- you add a banner, widget or animation that could hurt LCP, CLS or INP;
- you change caching, fingerprint assets or deploy: stale assets and stale pages share causes;
- you build carousels, sliders or hidden-until-scroll content that must stay crawlable and accessible.

`frontend-best-practices` calls this skill back whenever the pages will be public and indexed.

## References

- `references/sources.md` - every external claim with its URL and the date it was checked.
- `references/ai-crawlers.md` - the crawler table, a robots.txt template and what each token controls.
- `references/consent-and-tags.md` - consent defaults, load order, attribution capture, tag-ID formats and the tests.
- `LESSONS.md` - the dated, append-only lessons log.
