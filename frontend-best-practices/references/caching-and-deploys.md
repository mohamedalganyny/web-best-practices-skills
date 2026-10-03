# Caching, fingerprinting and deploys

Supports PER-2, PER-3, PER-9, PER-10 and the redirect and stale-page lessons in `seo-best-practices`. Static sites live and die on three things: what the browser caches, what the CDN caches, and what a deploy replaces.

## The cache contract

| Resource | Header | Condition |
|---|---|---|
| HTML documents | `Cache-Control: no-cache` (revalidate every time) | always |
| CSS, JS, fonts, images whose file name contains a content hash | `Cache-Control: public, max-age=31536000, immutable` | the name changes whenever the bytes change |
| Anything else (fixed-name files) | short `max-age` plus validation, or fingerprint it | never `immutable` |

`immutable` is a promise that the URL's bytes never change. Break the promise and returning visitors keep the old file for up to a year while a normal reload does not even ask the server.

Typical Apache-style rule (adapt to the host; only valid when every matched file is fingerprinted):

```apache
<IfModule mod_headers.c>
  <FilesMatch "\.(?:css|js|woff2|avif|webp)$">
    Header set Cache-Control "public, max-age=31536000, immutable"
  </FilesMatch>
  <FilesMatch "\.html$">
    Header set Cache-Control "no-cache"
  </FilesMatch>
</IfModule>
```

## Fingerprint every fixed-name file

Bundlers hash the files they emit, but plain files copied from a `public/` folder, override stylesheets, small runtime scripts and font-face stylesheets keep their names. Under the contract above they are stale forever.

Build step (runs after the site build, before deploy):

1. List the fixed-name assets explicitly (an array in the script).
2. For each, compute a short content hash and copy it to `name.<hash>.ext`.
3. Rewrite every reference in the built HTML (and in any CSS that links the file), then delete the unhashed original.
4. Fail the build if any HTML references a `.css` or `.js` whose name carries no hash and is not on an allow-list of files deliberately served without `immutable`.
5. Make adding a new fixed-name file a one-line change to the list, and document it where people add files.

Test it: build twice with a one-byte change to a listed file and assert that its name changed and that no page references the old one; build with an unlisted fixed-name file and assert that the build fails.

Symptom of getting this wrong: "I fixed it and it is still broken for client X". Customers on their second visit run last month's CSS. Staging looks right because a fresh profile has an empty cache.

## CDN HTML cache

Many hosts front every site with a CDN that caches HTML. After each deploy:

- purge the HTML cache (the host's API or panel), or send validators and keep HTML `no-cache`;
- confirm with `curl -sI https://example.com/ | grep -i -E "cache|age|etag"` and again with a cache-busting query;
- never judge a deploy by what your own browser shows: it may be the browser's cache, the CDN's, or both.

CDNs also tend to challenge automated headless browsers with an interstitial (HTTP 403) while serving real browsers and search crawlers the real page. So:

- verify crawler access with `curl -A "<the crawler's published user-agent string>" -I https://example.com/` and confirm 200;
- run browser tests against a local build, not the live site;
- drive live forms with a request, not a headless browser.

## What a deploy replaces

Two deploy styles exist and they fail in opposite ways.

| Style | Danger |
|---|---|
| Replace the whole web root with an archive | Deletes everything else in that root: sibling sub-sites, apps, verification files, mail autoconfig. A client's main domain often shares its root with sub-domains and apps. |
| Upload files one by one | Never deletes: a renamed or removed route keeps serving its old HTML, duplicate content with a self-canonical, forever. |

Rules:

1. List the target root before the first deploy to a domain. If other things live there, use per-file deploys only.
2. A renamed route needs three things: a redirect from the old URL, removal of the old file (or overwriting it with a stub), and an updated sitemap.
3. A stub, when removal is impossible, is an instant-refresh page carrying `<link rel="canonical" href="new URL">` and a plain link. Do not add `noindex` to it: it contradicts the canonical.
4. Keep a manifest of the files you deployed so the next deploy can compute what to remove.

## Redirects: test them live

Redirect directives differ by server and some are PREFIX matches. In Apache's `mod_alias`, `Redirect 301 /old /new` also sends `/old/anything` to `/new/anything`. Use `RedirectMatch 301 ^/old$ /new` for an exact match, and test a nested child path.

The same file, byte for byte, can behave differently on two hosts or for two locale prefixes. After every deploy, request every old URL (and one made-up child path under it) with `curl -sI` and assert status and `Location`. Do it per locale. A guard that only reads the config file proves nothing about the server.

## DNS and hosting side effects

Attaching a domain to a new site on a host can repoint that domain's DNS records within seconds (apex alias, `www` CNAME) and write a snapshot. A live site goes dark or changes owner at once.

- Stage the site on a temporary host name; verify; only then attach the real domain.
- Read the DNS records and snapshot list immediately after any host operation on a live domain.
- Keep mail records (MX, SPF, DKIM, DMARC) out of any reset or restore you did not mean to run.

## Things that look like caching bugs but are not

- A stylesheet that arrives before the framework's own CSS, so equal-specificity rules lose (CSS-1).
- A service worker you forgot you shipped.
- A file referenced by CSS (a mask image or font) that arrives late and moves `load` (PER-9).
- Browser "disk cache" versus "memory cache" in DevTools: use "Disable cache" plus a hard reload to compare.
