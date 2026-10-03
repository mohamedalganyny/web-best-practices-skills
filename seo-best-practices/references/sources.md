# Sources for seo-best-practices

Every external claim in `SKILL.md` and the references, with its URL and the date it was last checked.

Status codes:

- **V** - verified: the page was fetched on the `checked` date and the claim matches its wording.
- **E** - checked earlier (2026-09-27) against the primary source and not re-fetched since. Re-check on the next refresh.
- **S** - secondary source (trade press, community). Treat as a pointer, find the primary.
- **U** - unverified or low confidence; do not state to a client as fact.

Refresh procedure: when `last_verified` in `SKILL.md` is older than 30 days, re-fetch every source behind an item tagged `[fast]` (at least S1-S3, S11, S17-S21, S26-S29), compare the quoted claim, then update the item, its inline date, the `checked` cell here and finally `last_verified`. A Google page's own "last updated" date is recorded in the claim column when the page showed one.

## Search engines and Google documentation

| ID | Source | URL | Supports | Checked | Status |
|---|---|---|---|---|---|
| S1 | Google Search Central, Guide to optimizing for generative AI search (page updated 2026-07-10) | https://developers.google.com/search/docs/fundamentals/ai-optimization-guide | Eligibility = indexed and eligible to show with a snippet; "You don't need to create new machine readable files, AI text files, markup, or Markdown"; llms.txt is neither harm nor help and Google Search does not use it; no need to chunk content; no special schema; optimising for generative AI "is optimizing for the search experience, and thus still SEO" | 2026-10-03 | V |
| S2 | Google Search Central, AI features and your website (page updated 2025-12-10) | https://developers.google.com/search/docs/appearance/ai-features | No additional requirements to appear in AI Overviews or AI Mode; page must be indexed and snippet-eligible; no special structured data | 2026-10-03 | V |
| S3 | Google Search Central, Documentation updates | https://developers.google.com/search/updates | FAQ rich result: deprecation notice 2026-05-08 ("will no longer appear ... starting May 7, 2026"), documentation removed 2026-06-15; How-to removed 2023-09-14; review-snippet guideline on fake and undisclosed-incentive reviews added 2026-07-24; `VideoObject` `creator` added 2026-09-24 | 2026-10-03 | V |
| S4 | Google Search Central, Build and submit a sitemap | https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap | "Google ignores `<priority>` and `<changefreq>` values"; `<lastmod>` is used "if it's consistently and verifiably ... accurate" | 2026-10-03 | V |
| S5 | Google Search Central, Localized versions of your pages | https://developers.google.com/search/docs/specialty/international/localized-versions | Reciprocal return links, every version lists itself and all others, `x-default` as the fallback, fallback page for auto-redirecting home pages | 2026-10-03 | V |
| S6 | Google Search Central, Introduction to robots.txt | https://developers.google.com/search/docs/crawling-indexing/robots/intro | robots.txt manages crawling and is "not a mechanism for keeping a web page out of Google"; use `noindex` or password protection | 2026-10-03 | V |
| S7 | Google Search Central, Consolidate duplicate URLs (canonical) | https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls | Self-referential canonical recommended; `rel="canonical"` is a strong signal; absolute paths | 2026-10-03 | V |
| S8 | Google Search Central, Title links | https://developers.google.com/search/docs/appearance/title-link | No limit on `<title>` length; truncated as needed; title links are generated automatically and may differ from the element | 2026-10-03 | V |
| S9 | Google Search Central, Snippets and meta descriptions | https://developers.google.com/search/docs/appearance/snippet | Snippets come mainly from page content; the meta description is used sometimes when it describes the page better | 2026-10-03 | V |
| S10 | Google Search Central, Spam policies | https://developers.google.com/search/docs/essentials/spam-policies | Scaled content abuse (many pages without added value, including via generative AI); site reputation abuse (third-party content hosted for the host's ranking signals); paid links need `rel="nofollow"` or `rel="sponsored"`; doorway abuse (multiple region or city pages funnelling to one page) | 2026-10-03 | V |
| S11 | Google Search Central, Review snippet (page updated 2026-09-08) | https://developers.google.com/search/docs/appearance/structured-data/review-snippet | Entity-controlled reviews on `LocalBusiness` or `Organization` pages are ineligible for stars; reviews written in exchange for undisclosed benefits and non-genuine reviews are prohibited | 2026-10-03 | V |
| S12 | Google Search Central, General structured data guidelines | https://developers.google.com/search/docs/appearance/structured-data/sd-policies | Do not mark up invisible content; markup must truly represent the page; issues can cause a manual action | 2026-10-03 | V |
| S13 | Google Search Central, Organization structured data (page updated 2026-09-08) | https://developers.google.com/search/docs/appearance/structured-data/organization | Recommended properties `legalName`, `taxID`, `address`, `sameAs`, `vatID` | 2026-10-03 | V |
| S14 | Google Search Central, Creating helpful, reliable, people-first content | https://developers.google.com/search/docs/fundamentals/creating-helpful-content | Who, How, Why; bylines that lead to author background; E-E-A-T with trust most important; more weight on strong E-E-A-T for topics affecting health, finances or safety | 2026-10-03 | V |
| S15 | Google Search Central, Video best practices | https://developers.google.com/search/docs/appearance/video | A watch page per video; `VideoObject` consistent with the video; crawlable `<video>`, `<embed>`, `<iframe>`, `<object>` | 2026-10-03 | V |
| S16 | Google Search Central, Link best practices (crawlable links) | https://developers.google.com/search/docs/crawling-indexing/links-crawlable | Links as a discovery and relevance signal; anchor text tells users and Google about the target; every important page should be linked from at least one other | 2026-10-03 | V |
| S17 | Google Crawling infrastructure, Common crawlers | https://developers.google.com/crawling/docs/crawlers-fetchers/google-common-crawlers | `Google-Extended` manages use of crawled content for training Gemini models and does not affect Search inclusion or ranking | 2026-10-03 | V |
| S29 | Google Search Central, Structured data search gallery | https://developers.google.com/search/docs/appearance/structured-data/search-gallery | Features listed: Article, Breadcrumb, Carousel, Course list, Dataset, Discussion forum, Education Q&A, Employer aggregate rating, Event, Image metadata, Job posting, Local business, Math solver, Movie, Organization, Product, Profile page, Q&A, Recipe, Review snippet, Software app, Speakable, Subscription and paywalled content, Vacation rental, Video. FAQ and How-to are not listed | 2026-10-03 | V |
| S30 | Google Search Central, URL structure best practices | https://developers.google.com/search/docs/crawling-indexing/url-structure | Non-ASCII characters in URLs should be percent-encoded; readable words over IDs; hyphens to separate words | 2026-10-03 | V |
| S31 | Search Console Help, Disavow links | https://support.google.com/webmasters/answer/2648487 | Advanced feature, use with caution; for substantial spammy links that caused or will likely cause a manual action | 2026-10-03 | V |
| S32 | Search Console Help, Page indexing report | https://support.google.com/webmasters/answer/7440203 | The report shows which pages are indexed and the problems found. That the old "Crawl errors" report is gone, and the Crawl stats and URL Inspection tools, come from the 2026-09-27 review | 2026-10-03 | V for the report; E for the rest |
| S34 | Google Search Central, Block indexing with noindex | https://developers.google.com/search/docs/crawling-indexing/block-indexing | `noindex` meta tag or `X-Robots-Tag`; the page must not be blocked by robots.txt or the crawler never sees the rule | 2026-10-03 | V |
| S35 | Google Search Central, Image SEO best practices | https://developers.google.com/search/docs/appearance/google-images | Do not keyword-stuff `alt`; descriptive file names; supported formats BMP, GIF, JPEG, PNG, WebP, SVG, AVIF; no EXIF or geotag factor mentioned | 2026-10-03 | V |
| S36 | Google Search Central, Publication dates | https://developers.google.com/search/docs/appearance/publication-dates | Show a visible date; `datePublished` and `dateModified`; keep visible and structured dates consistent; no future or fabricated dates | 2026-10-03 | V |
| S38 | Google Search Central, Site move with URL changes | https://developers.google.com/search/docs/crawling-indexing/site-move-with-url-changes | Use permanent server-side redirects (301, 308); keep them "as long as possible, generally at least 1 year" | 2026-10-03 | V |

## Performance

| ID | Source | URL | Supports | Checked | Status |
|---|---|---|---|---|---|
| S22 | web.dev, Web Vitals | https://web.dev/articles/vitals | LCP up to 2.5 s, INP up to 200 ms, CLS up to 0.1; measure at the 75th percentile, segmented across mobile and desktop | 2026-10-03 | V |
| S23 | web.dev, Interaction to Next Paint | https://web.dev/articles/inp | INP is the successor to First Input Delay. The replacement date (March 2024) is from the 2026-09-27 review; see also https://web.dev/blog/inp-cwv-launch | 2026-10-03 | V for the successor claim; E for the date |

## AI crawlers and agent readiness

| ID | Source | URL | Supports | Checked | Status |
|---|---|---|---|---|---|
| S18 | OpenAI, Overview of OpenAI crawlers | https://developers.openai.com/api/docs/bots | OAI-SearchBot (search, robots.txt applies), GPTBot (training, robots.txt applies), OAI-AdsBot (validates ads, robots.txt does not apply), ChatGPT-User (user actions, robots.txt may not apply) | 2026-10-03 | V |
| S19 | Anthropic Support, Does Anthropic crawl data from the web | https://support.claude.com/en/articles/8896518 | ClaudeBot (training), Claude-User (user requests), Claude-SearchBot (search quality); all respect robots.txt | 2026-10-03 | V |
| S20 | Perplexity, Crawlers | https://docs.perplexity.ai/guides/bots | PerplexityBot surfaces and links sites in search results, is not used for foundation-model training, and should be allowed; Perplexity-User is user-initiated and generally ignores robots.txt | 2026-10-03 | V |
| S21 | Apple Support, About Applebot | https://support.apple.com/en-us/119829 | Applebot powers Spotlight, Siri and Safari features; `Applebot-Extended` does not crawl and only governs training use; disallowing it does not remove pages from search | 2026-10-03 | V |
| S24 | IndexNow, FAQ | https://www.indexnow.org/faq | Participating engines listed: Amazon, Bing, Naver, Seznam.cz, Yandex, Yep; Google is not listed | 2026-10-03 | V |
| S37 | IETF, RFC 9727 api-catalog | https://www.rfc-editor.org/rfc/rfc9727 | Defines `/.well-known/api-catalog` for discovery of public APIs | 2026-10-03 | V |
| S39 | Cloudflare, content-signal proposal and agent-readiness tests | https://contentsignals.org/ and https://isitagentready.com/ | The optional `Content-Signal` robots.txt line (`search`, `ai-input`, `ai-train`); adoption figures for agent-readiness features (April 2026 launch) | 2026-10-03 | U (isitagentready.com exists and tests robots.txt, sitemap, link headers, Markdown negotiation, MCP and OAuth discovery; contentsignals.org returned only a heading, so the signal names are unconfirmed; no documented engine honours the line) |
| S43 | Web Machine Learning Community Group, WebMCP repository | https://github.com/webmachinelearning/webmcp | A community-group proposal (not a W3C standard) that lets a page expose JavaScript functions or HTML forms as tools for AI agents; the spec is still evolving. Browser origin-trial status is reported in trade press only | 2026-10-03 | V for what it is; U for browser status |
| S44 | IETF, RFC 9728 (OAuth protected resource metadata) and RFC 8414 (OAuth authorization server metadata) | https://www.rfc-editor.org/rfc/rfc9728 and https://www.rfc-editor.org/rfc/rfc8414 | Metadata discovery documents for APIs with OAuth | 2026-09-27 | E |
| S41 | Search Engine Land, reports on Markdown versions served to bots (February 2026) | https://searchengineland.com/ | Google and Bing representatives publicly discouraged separate Markdown pages for bots as a cloaking risk | 2026-09-27 | S (no single primary URL recorded) |

## Local, reviews, consent and tags

| ID | Source | URL | Supports | Checked | Status |
|---|---|---|---|---|---|
| S25 | Google Business Profile Help, Improve your local ranking | https://support.google.com/business/answer/7091 | Relevance, distance, prominence; "There's no way to request or pay for a better local ranking on Google" | 2026-10-03 | V |
| S33 | Google Maps, Prohibited and restricted content: fake engagement | https://support.google.com/contributionpolicy/answer/7400114 | Incentivised reviews, selectively soliciting positive reviews or discouraging negative ones, and paid reviews are prohibited | 2026-10-03 | V |
| S26 | Google tag platform, Consent mode | https://developers.google.com/tag-platform/security/guides/consent | Set defaults with `gtag('consent', 'default', ...)` before any measurement command; update on user interaction; v2 parameters `ad_storage`, `analytics_storage`, `ad_user_data`, `ad_personalization` | 2026-10-03 | V |
| S27 | Meta for Developers, Handling duplicate pixel and server events | https://developers.facebook.com/docs/marketing-api/conversions-api/deduplicate-pixel-and-server-events | Pixel `eventID` must match server `event_id`, and `event` must match `event_name`; duplicates within 48 hours are discarded | 2026-10-03 | V |
| S28 | Microsoft Learn, Clarity consent mode | https://learn.microsoft.com/en-us/clarity/setup-and-installation/consent-mode | Consent signal enforcement for EEA, UK and Switzerland visits from 2025-10-31 | 2026-10-03 | V |
| S40 | Secondary summaries of national data-protection regulations (for example Egypt's PDPL executive regulations, reported late 2025, with a roughly one-year grace period) | none recorded | Prior consent is required in a growing number of jurisdictions | 2026-09-27 | S/U - verify the law of each audience country |
| S45 | Ad platform landing-page review criteria (OpenAI and others) | none recorded | Copy matches the page, working form, visible privacy policy and business identity, substantiated claims | 2026-09-27 | U - read the current ad policy of the platform you run |
| S42 | Press reports on the journalist-request service relaunch (December 2024 shutdown, April 2025 relaunch under a new operator) | none recorded | Treat such services as PR, not link building | 2026-09-27 | S/U |

## Rules that are conventions, not sourced claims

Tagged `[C]` in `SKILL.md`. They are house rules that have worked in practice and carry no primary source: mobile-first parity, one canonical host, single `<h1>` convention, build-time crawl checks, direct-answer openings, funnel order, cluster requirement, agent drafts never auto-publish, structured data generated from records, the four questions for every page, measurement cadence, one source of truth for NAP and prices, and typed tracking IDs instead of raw script fields. Re-examine them when a primary source starts to address them.
