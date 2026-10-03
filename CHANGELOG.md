# Changelog

All notable changes to these skills are listed here, newest first. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the skills use [Semantic Versioning](https://semver.org/): a new or changed rule is a minor version, a wording fix is a patch, and removing a rule or renaming a rule ID is a major version.

Agents following the self-update protocol add a line under `Unreleased` whenever they add, change or remove a rule, and say which skill and rule ID it touched.

## Unreleased

## 1.0.0 - 2026-10-03

First public release.

### Added

- `frontend-best-practices`: 69 rules in six groups (layout and RTL, motion and accessibility, performance and caching, CSS pipeline, components, testing and verification), a "before you start" and a "before you call it done" checklist, and a self-update protocol. References for RTL and bilingual sites, motion, accessible carousels, caching and deploys, CSS pipeline pitfalls, verification, and dated sources.
- `frontend-best-practices/LESSONS.md`: seeded with the lessons from the first builds the rules came from, generalised and de-identified.
- `seo-best-practices`: 82 items in ten groups (technical, international, on-page, content, structured data, AI search, local, off-page, privacy and tags, measurement), a myths list, a pre-publish checklist and a self-update protocol. Every item carries its source date. References for sources, AI crawlers and consent and tags.
- `seo-best-practices/LESSONS.md`: seeded from the source-vetting review and from deploy and redirect incidents.
- Cross-references so each skill tells the agent when to call the other.

### Verified at release

- 2026-10-03: primary documentation re-fetched for every `[fast]` item (Google Search Central guidance and updates, AI crawler operator pages, web.dev, WCAG 2.2, W3C ARIA Authoring Practices, MDN, vendor consent documentation). Findings: Google's AI-search guidance needs no special files or markup; FAQ rich results ended on 2026-05-07; non-ASCII URL slugs should be percent-encoded.

### Known gaps

- Items marked unverified or secondary in each `references/sources.md` (regional privacy-law details, ad-platform landing-page criteria, press reports on bot-targeted Markdown pages, the optional `Content-Signal` robots.txt line) need a primary source.
- Browser support for scroll-driven CSS animations is recorded as "not Baseline"; re-check the compatibility table at each refresh.
