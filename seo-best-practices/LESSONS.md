# Lessons log - seo-best-practices

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

### 2026-09-27 · A plan still counted on FAQ rich results
- Symptom: a site plan listed FAQ and How-to markup as a way to win rich results.
- Cause: the plan predated the changes; Google removed How-to results in 2023 and stopped showing FAQ rich results on 2026-05-07 (documentation removed 2026-06-15).
- Rule: SCHEMA-3. Keep FAQ markup only as harmless entity data, or drop it; keep the visible FAQ for readers (PAGE-3). Check the structured-data gallery before promising a rich result.

### 2026-09-27 · "Special" optimisation for AI Overviews does not exist
- Symptom: plans included an AI-specific file, Markdown copies of pages, chunking and special schema.
- Cause: folklore. Google's own guide says eligibility is being indexed and snippet-eligible, that no new machine-readable files, markup or Markdown are needed, and that optimising for generative AI "is still SEO". Its llms.txt statement: neither harm nor help.
- Rule: AI-1, AI-7, AI-8. Spend the effort on crawlable, fast, clear pages and entity consistency.

### 2026-09-27 · Folk rules with no documented basis
- Symptom: a checklist demanded the focus keyword within the first 100 words and weekly publishing "for ranking".
- Cause: both are habits, not documented ranking rules; weekly output without added value risks scaled-content abuse.
- Rule: PAGE-1 (say what the page is and who it serves, early, in the reader's words), CONT-3 (cadence is a business choice).

### 2026-09-27 · Marketing-copy rules versus the privacy law
- Symptom: a house rule said public pages never name the tools behind them, which would have left the privacy and cookie policies vague.
- Cause: privacy law requires naming the tracking tools a site actually uses; marketing copy is a different thing.
- Rule: TAG-3 and `references/consent-and-tags.md`. Marketing pages can stay vendor-neutral; the policies list exactly the tools switched on.

### 2026-09-27 · A site's own review stars
- Symptom: a plan marked up customer reviews on the business's own pages to get stars.
- Cause: reviews controlled by the entity being reviewed make `LocalBusiness` or `Organization` pages ineligible for the star feature; in July 2026 Google also added a guideline against fake and undisclosed-incentive reviews.
- Rule: SCHEMA-5, LOC-4. Show reviews as page content if you like; do not mark them up as the entity's own stars, and never gate or incentivise them.

### 2026-09-28 · Redirects must be tested live, per locale
- Symptom: after a deploy, one old path redirected and a sibling in a locale subfolder did not, with the same redirect file byte for byte.
- Cause: unknown at the time. A "first line is skipped" theory was disproved only after a fix had shipped. The server's behaviour differed per path, so reading the file proved nothing.
- Rule: TECH-14. After every deploy, `curl` every old URL, including a made-up child path, in every locale. When a redirect cannot be made to work, overwrite the stale files with an instant-refresh page carrying `rel="canonical"` to the new URL (no `noindex`: it contradicts the canonical) and log the debt.

### 2026-09-28 · Stale pages survive file-by-file deploys
- Symptom: after renaming a route, the old URL kept serving its old page next to the new one.
- Cause: a deploy that only uploads and never deletes leaves every renamed route's old HTML live, with a self-canonical, i.e. duplicate content.
- Rule: TECH-15. A rename needs a redirect AND removal (or a stub) of the old files; keep a manifest of deployed files.

### 2026-09-28 · Business identity for ads and trust
- Symptom: an ad platform review and a quality review both wanted proof of who runs the business.
- Cause: the site showed a brand name but not the legal name, registered address or tax registration number.
- Rule: CONT-7, SCHEMA-6. Show them on terms and privacy pages and in `Organization` markup (`legalName`, `address`, `taxID`). Ad copy must match the landing page, the form must work, and claims must be substantiated (AI-11).

### 2026-09-28 · Articles that make legal claims
- Symptom: a draft article stated what the advertising rules allow, from memory.
- Cause: legal claims written without primary sources are the riskiest content a site can publish.
- Rule: CONT-8. Research from primary sources first, keep the sources, and mark the article "not legal advice".

### 2026-10-03 · A redirect directive matched more than intended (seed)
- Symptom: a redirect for one path also sent its child paths to the wrong place.
- Cause: in Apache-style `Redirect`, the match is a prefix: the rest of the path is appended to the target.
- Rule: TECH-14. Use an exact-match form for single paths and test a nested child path live.

### 2026-10-03 · Editor-entered redirects can leave the site (seed)
- Symptom: a plan listed "redirects pointing anywhere an editor types" as a feature.
- Cause: unrestricted targets are an open redirect; absolute targets, query-string sources, loops and cycles also break crawling.
- Rule: TECH-14. Keep only https targets on the site's own host, drop the rest at build time and log each drop.

### 2026-10-03 · Hosting side effects on a live domain (seed)
- Symptom: creating a website on a host repointed a live domain's DNS within seconds.
- Cause: the host auto-configures DNS when a domain is attached.
- Rule: TECH-19. Stage on a temporary name, verify, then attach; read the DNS records straight after any host operation.

### 2026-10-03 · A CDN served crawlers and blocked the test browser (seed)
- Symptom: a headless browser got a 403 interstitial from the live site while real browsers and search crawlers got the page.
- Cause: the host's CDN challenges automation.
- Rule: TECH-18. Verify crawler access with `curl -A` and the crawler's user agent; run browser tests on a local build; drive live forms with requests.

### 2026-10-03 · Non-ASCII slugs: encode them (refresh finding)
- Symptom: while re-checking sources, the old note "Arabic UTF-8 slugs are fine" turned out to be incomplete.
- Cause: Google's URL guidance says non-ASCII characters in URLs should be percent-encoded (the browser still shows the readable form).
- Rule: TECH-8. Slugs in any script are fine; emit one consistent percent-encoded form in links, canonicals, sitemaps and hreflang.
- Replaces: "Arabic UTF-8 slugs: fine" without the encoding qualifier.

### 2026-10-03 · Programmatic and AI-assisted pages (seed)
- Symptom: a plan produced many near-identical pages from templates and a model.
- Cause: pages without unique value fall under scaled content abuse; location variants that funnel to one page are doorways.
- Rule: CONT-3, LOC-3. Generate only pages that carry unique data or experience (real locations, real services), draft them for human review and never auto-publish.

### 2026-10-03 · Breadcrumbs built from URL segments (build)
- Symptom: generated BreadcrumbList data linked to a parent path that the site never builds (a profile page under `/team/` with no `/team` index) and used raw slugs or hard-coded labels that did not match the site's own navigation in the second language.
- Cause: the crumb trail was derived from the URL string, not from the routes the build emits or the site's own page titles.
- Rule: SCHEMA. Build breadcrumbs from the list of routes the build actually produces; label each crumb with the title of that page in the current locale, skip any parent without a page, and assert in a test that every crumb URL resolves to a built file.

### 2026-10-03 · SEO fields the CMS silently dropped (build)
- Symptom: new meta titles and descriptions were written by the seed for one content type, the tests passed, and the live pages still showed the old text.
- Cause: that content type was never registered with the CMS's SEO-fields plugin, so the field did not exist and the write was discarded without an error; the tests fed fixture data straight to the renderer.
- Rule: TECH and on-page checks. After seeding or editing metadata, check the loaded content or a built page, not the seed; when a template reads `meta` on a content type, assert the CMS defines that field.

### 2026-10-03 · A launch-day IndexNow ping (build)
- Symptom: none (it worked); recording the shape that did.
- Cause: n/a.
- Rule: TECH-7. Publish the key file at the root, deploy, confirm the key URL returns 200, then submit only the changed URLs in one POST; an HTTP 202 means accepted, pending key validation.

## Retired or corrected rules

None yet. (The non-ASCII slug entry above refines TECH-8; the rule text was updated in the same change.)
