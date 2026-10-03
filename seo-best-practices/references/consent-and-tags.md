# Consent, tags and attribution

Supports TAG-1 to TAG-9. Goal: marketing tags that never cost Core Web Vitals, never run before consent, never become a code-execution hole, and still attribute leads correctly. Consent Mode, Meta deduplication and the Clarity rule were checked on 2026-10-03 (`sources.md`, S26-S28). Everything else here is a design that worked, tagged accordingly.

## Design decisions

1. **Typed IDs, never raw HTML.** A site-settings "tracking" group stores identifiers; the build emits known loaders. A free-text script field is stored XSS for anyone who can edit settings and forces `unsafe-inline` into the content-security policy. With typed IDs the build can also emit a per-site CSP allow-list.
2. **Tag-manager containers are arbitrary code.** Whoever can publish the container can run anything on the site. Offer it only when the client insists; prefer direct tags. A raw `customHeadHtml` field, if it must exist, is admin-only, empty by default, reviewed and hashed into the CSP.
3. **Settings are editable only by the agency role;** client editors see them read-only. Server-side secrets (API tokens for server events) live in the gateway's environment, never in the CMS or the page.
4. **One banner for every visitor, everything denied by default.** Regional logic is a liability when privacy law keeps spreading; a single behaviour is simpler to test and to explain.
5. **No `<noscript>` tracking pixels:** they fire without consent.
6. **The privacy and cookie policies list exactly the tools the site has switched on.** Marketing copy elsewhere need not name vendors.

## Consent defaults (Consent Mode v2)

```js
window.dataLayer = window.dataLayer || [];
function gtag() { dataLayer.push(arguments); }

// Before ANY measurement command, on every page:
gtag('consent', 'default', {
  ad_storage: 'denied',
  analytics_storage: 'denied',
  ad_user_data: 'denied',
  ad_personalization: 'denied',
  wait_for_update: 500,
});

// When the visitor chooses:
function onAccept() {
  gtag('consent', 'update', {
    ad_storage: 'granted',
    analytics_storage: 'granted',
    ad_user_data: 'granted',
    ad_personalization: 'granted',
  });
}
```

"Basic" mode (the one recommended here) means no vendor script loads at all until the click; "advanced" mode loads tags that send cookieless pings and is a legal decision, not a technical default. Offer granular choices (analytics only, marketing too) if the site's legal advice requires them.

## Loading order

1. An inline bootstrap, hashed in the CSP, holds the enabled IDs and the consent defaults.
2. A fixed-position banner: it causes no layout shift and is never the LCP element.
3. On accept, load vendors only after the page's `load` event plus `requestIdleCallback` (3 s timeout), or on the first interaction:

```js
const idle = window.requestIdleCallback || ((fn) => setTimeout(fn, 1));
function afterLoad(fn) {
  const run = () => idle(fn, { timeout: 3000 });
  document.readyState === 'complete' ? run() : addEventListener('load', run, { once: true });
}
```

4. Do not use a worker-offloading library by default. Consider server-side tagging later if volume justifies it. Check whether the CDN in front of a static host can proxy a tag gateway before planning one.
5. Remember a decision per browser (a first-party cookie or storage key with a sensible lifetime) and offer a visible way to change it from the footer.

## Vendor consent rules to re-check

- Session-replay and behaviour-analytics tools may require a consent signal for some regions (one tool has enforced it for EEA, UK and Switzerland visits since 2025-10-31): configure the vendor's consent mode or API and verify no cookies are set before the click. `[S28]`
- Ad platforms' own consent-signal requirements change often; re-read them on each refresh.

## Attribution capture (after consent only)

Capture on landing: UTM parameters; click ids `gclid`, `gbraid`, `wbraid`, `fbclid`, `ttclid`, `msclkid`, `li_fat_id`, `ScCid`; the landing URL; the first-touch timestamp. Persist them for the visit (first-party storage), and submit them with every form and booking.

Meta's browser id from a click: build `_fbc` as `fb.1.<timestamp-ms>.<fbclid>` and keep the `fbclid` case exactly as received.

## One conversion, one event id

- Generate a UUID `event_id` when the visitor submits the form.
- Send it to the browser pixel as `eventID` and to your server as `event_id`, with the identical event name.
- The server sends its own event to the vendor's server API only when `ad_user_data` consent was granted.
- Meta discards the later of a matching pair received within 48 hours. `[S27]`
- Debug with the vendors' test tools (a `test_event_code` supplied by the server, a pixel helper, a tag-assistant session, analytics debug view).

## Tag-ID fields and observed formats

Patterns are observed formats, kept permissive on purpose. A vendor can change a format without notice: validate on save with a warning, not a hard failure, unless you have confirmed the current rule.

| Field | Observed format |
|---|---|
| consent mode | `banner-all` (default) or `banner-eea-only` |
| tag-manager container id | `GTM-` followed by 5-10 uppercase letters or digits |
| analytics measurement id | `G-` followed by 6-12 uppercase letters or digits |
| ads account id | `AW-` followed by 9-12 digits |
| ads conversions | a list of event and label pairs; the event is lead, booking, call or whatsapp; the label is 8-40 letters, digits, underscores or hyphens |
| social pixel id (Meta) | 15-17 digits |
| short-video platform pixel id | 20 uppercase letters or digits |
| professional-network partner id | 4-10 digits, plus conversion ids of 6-12 digits |
| behaviour-analytics project id | 8-12 lowercase letters or digits |
| snap pixel id | a UUID |
| ad network UET tag id | 6-10 digits |
| search-console verification token | 20-64 letters, digits, underscores or hyphens (a static meta tag, no consent) |
| Bing verification token | 32 uppercase hex characters |
| domain verification token | 20-40 lowercase letters or digits |
| custom head HTML | admin only, empty, reviewed, CSP-hashed |

## Tests

1. **Rejected banner means zero vendor requests.** On a LOCAL build (a CDN may block headless browsers on the live site), load the page, reject, wait for `load` plus 5 s, and assert that no request went to a deny-list of vendor hosts and that no vendor cookies exist.
2. **Accepted banner loads exactly the enabled tools,** after `load`, with no console errors.
3. **LCP and CLS unchanged** with the banner shown (measure both banner states in the performance budget).
4. **Event id matches** between the browser event and the server event for one submission (assert in the gateway's test).
5. Break each on purpose: add a hard-coded vendor script (test 1 must fail), move the load call before the click (test 1 must fail), drop the `event_id` from the server event (test 4 must fail).

## Verifying manually

- Vendor debug tools on the staging build, with consent given, then withdrawn.
- A fresh browser profile, to see the first-visit behaviour a regulator would see.
- The cookie list in the policy against the cookies actually set after accept.
