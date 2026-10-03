# AI crawlers and robots.txt

Supports AI-4 to AI-8. Operator documentation was checked on 2026-10-03 (see `sources.md`, S17-S21). Crawler names change: re-check the operator pages whenever this skill is stale, and add new tokens as dated lessons.

## The table

| Token | Operator | Purpose | Does robots.txt apply? | Default decision |
|---|---|---|---|---|
| Googlebot | Google | Search crawling and indexing (also the basis for AI Overviews and AI Mode) | Yes | Allow |
| OAI-SearchBot | OpenAI | Surfaces sites in ChatGPT search results | Yes | Allow |
| ChatGPT-User | OpenAI | Fetches a page when a user asks ChatGPT about it | Operator says rules may not apply | Allow |
| OAI-AdsBot | OpenAI | Validates pages submitted as ads on ChatGPT | No | Allow if you run ads there |
| GPTBot | OpenAI | May crawl content used in training | Yes | Owner's decision |
| Claude-SearchBot | Anthropic | Improves search result quality | Yes | Allow |
| Claude-User | Anthropic | Fetches pages when a user asks Claude | Yes | Allow |
| ClaudeBot | Anthropic | Collects web content for training | Yes | Owner's decision |
| PerplexityBot | Perplexity | Surfaces and links sites in Perplexity search; not used for foundation-model training | Yes (allow it) | Allow |
| Perplexity-User | Perplexity | User-initiated fetch | Generally ignores it | Allow |
| Google-Extended | Google | A token controlling whether crawled content may train Gemini models; it does not affect Search inclusion or ranking | Yes (token only) | Owner's decision |
| Applebot | Apple | Search features in Spotlight, Siri and Safari | Yes | Allow |
| Applebot-Extended | Apple | Does not crawl; only governs whether content may train Apple's models | Yes (token only) | Owner's decision |

"Owner's decision" means the site owner chooses allow or opt-out, and the choice is recorded per site (a setting, not a code branch). Search and user-fetch agents are allowed by default because blocking them removes the site from AI answers.

## A robots.txt template

```
# Everyone: crawl the site, keep admin areas out of the crawl.
User-agent: *
Disallow: /admin/
Sitemap: https://example.com/sitemap-index.xml

# Search and user-fetch agents: allowed. Each named group must repeat any Disallow
# lines, because a crawler obeys only the most specific group that names it.
User-agent: Googlebot
Disallow: /admin/

User-agent: OAI-SearchBot
Disallow: /admin/

User-agent: ChatGPT-User
Disallow: /admin/

User-agent: Claude-SearchBot
Disallow: /admin/

User-agent: Claude-User
Disallow: /admin/

User-agent: PerplexityBot
Disallow: /admin/

# Training agents: per the site owner's decision (shown as allowed; use "Disallow: /" to opt out).
User-agent: GPTBot
Disallow: /admin/

User-agent: ClaudeBot
Disallow: /admin/

User-agent: Google-Extended
Disallow: /admin/

User-agent: Applebot-Extended
Disallow: /admin/
```

Notes:

- An empty `Disallow:` line (or none) allows everything for that group. Repeating the `/admin/` line keeps private areas private when a named group would otherwise replace the `*` group.
- Name every token explicitly, not only `*`: some operators and auditors look for the names, and a later opt-out is a one-line edit.
- An optional extra line under `User-agent: *` such as `Content-Signal: search=yes, ai-input=yes, ai-train=no` is a proposal from a CDN vendor. No documented engine honours it yet (sources.md S39, unverified); harmless to add, never rely on it.
- `Sitemap:` takes an absolute URL and may appear more than once.
- To remove a page from search use `noindex` on a crawlable page, not a `Disallow`.

## What robots.txt cannot do

- It is not access control. User-initiated fetches (ChatGPT-User, Perplexity-User) and the ads checker may not follow it. Put private material behind authentication.
- It does not remove pages that are already indexed or linked from elsewhere.
- It does not keep a page out of AI answers when that page is public and quoted elsewhere.

## Verifying

1. `curl -s https://example.com/robots.txt` returns 200 with the expected groups and the sitemap line.
2. `curl -I -A "<crawler's published user-agent string>" https://example.com/` returns 200 and the same HTML a visitor gets (a CDN may challenge headless browsers but should serve known crawlers).
3. Re-check the operators' pages monthly; log any new or renamed token as a lesson.
4. Serve the same content to every agent. Per-bot Markdown versions draw a cloaking concern (S41, secondary).

## llms.txt and friends

- `llms.txt`: Google states it neither helps nor harms and does not use it; no major engine documents using it. Publish a short, accurate one if it is cheap, and keep it consistent with the site.
- API catalog (`/.well-known/api-catalog`, RFC 9727), OAuth metadata (RFC 9728, RFC 8414), a form-tool pilot: tier 2, only when the site has an API, booking or checkout.
- Markdown negotiation, new discovery or bot-authentication drafts, agent payment protocols: watch only.
