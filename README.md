# agent-skills

Two linked, self-updating skills for AI coding agents and the people who work with them:

| Skill | Call it when | It covers |
|---|---|---|
| [`frontend-best-practices`](frontend-best-practices/SKILL.md) | You build, redesign or review any website or landing page | Layout and full-bleed, right-to-left and bilingual sites, motion and accessibility, performance and caching, CSS pipeline traps, components (headers, carousels, logo walls), testing and verification |
| [`seo-best-practices`](seo-best-practices/SKILL.md) | You plan, build, audit or publish pages that should be found by search engines and AI assistants | Technical SEO, international and hreflang, on-page, content, structured data, AI search (AIO, GEO, AEO), local search, off-page, consent and tracking tags, measurement |

They reference each other: the frontend skill tells the agent when to open the SEO skill, and the SEO skill tells it when to open the frontend skill. Install them together.

## Who it is for

- Developers and agencies who ship public sites, especially multilingual sites that include right-to-left languages such as Arabic.
- Anyone who delegates web work to an AI coding agent and wants it to apply the same hard-won rules every time, with the reasons attached, instead of rediscovering them.
- Reviewers who want a done-gate they can check against the built site rather than the source code.

## What makes them different

1. **Every rule has its reason.** One or two lines of rule plus why, so an exception can be judged instead of obeyed blindly.
2. **Every external claim is dated and sourced.** `references/sources.md` in each skill lists the URL, what it supports and the day it was last checked. Guidance that changes often is tagged `[fast]`.
3. **They update themselves.** Each time an agent uses a skill it follows a written protocol: re-check stale `[fast]` items, read the lessons log, and append new lessons after the work. See below.
4. **They say when to call them.** The `description` in each `SKILL.md` frontmatter and a "When to call this skill" section tell the agent (and you) exactly which moments need them.
5. **Lessons, not folklore.** The `LESSONS.md` files are real symptom, cause, rule entries from real builds, generalised and stripped of names.

## Layout

```
agent-skills/
  README.md
  LICENSE
  CHANGELOG.md
  frontend-best-practices/
    SKILL.md            rules grouped by topic, checklists, self-update protocol
    LESSONS.md          dated, append-only lessons log
    references/         rtl-and-bilingual, motion, carousels, caching-and-deploys,
                        css-pipeline, verification, sources
  seo-best-practices/
    SKILL.md            rules grouped by topic, checklists, self-update protocol
    LESSONS.md          dated, append-only lessons log
    references/         sources, ai-crawlers, consent-and-tags
```

## Install

The skills are plain Markdown with a small YAML frontmatter. Copy BOTH folders:

- **Claude Code, for every project:** copy `frontend-best-practices/` and `seo-best-practices/` into `~/.claude/skills/` (on Windows, `%USERPROFILE%\.claude\skills\`).
- **Claude Code, for one project:** copy them into `<project>/.claude/skills/` and commit them with the project, so the whole team (and CI agents) share the same lessons.
- **Other agents and tools:** most coding agents can load instruction files from a folder or accept them as context. Point the agent at the `SKILL.md` files, or paste them into its project instructions, and keep the `references/` folders next to them because the skills link to those paths.

Do not rename the folders: the skills refer to each other by folder name.

Then tell your agent, once: "Use `frontend-best-practices` whenever you build or review a website, and `seo-best-practices` whenever pages should be found in search." The skills also ask the agent to save a one-line pointer to its own memory the first time it uses them, if it has persistent memory.

## How the self-update works

Both `SKILL.md` files contain the same protocol, five numbered steps, which the agent follows every time it uses the skill:

1. **Freshness check.** Read `last_verified` in the frontmatter. If it is more than 30 days old, re-check the items tagged `[fast]` (search-engine guidance, supported structured-data features, AI crawler names, browser support, accessibility criteria) by searching the web or fetching the sources in `references/sources.md`, then update the rule, the source's checked date and `last_verified`. Without web access the agent says so and changes no dates.
2. **Apply the lessons log.** Read `LESSONS.md` before starting.
3. **Log new lessons.** After the work, append any lesson that generalises (date, symptom, cause, rule) to `LESSONS.md`. A lesson that changes a rule also edits the rule and adds a line to `CHANGELOG.md`, keeping the old wording in the lesson.
4. **Leave a pointer** in the agent's memory saying the skill exists and when to call it.
5. **Never store secrets, client names, private URLs, internal paths or personal data** in a skill, a lesson or a memory pointer. This step governs the other four.

Things to know:

- The agent edits the installed copy in place. If the copy lives in a project repository you will see the changes in `git diff` and can review them like any other change. If it is your global copy, keep it in a repository of its own so you can review and revert.
- Updates are local to your copy. To share an improvement, send the sanitised lesson or corrected rule back (see Contributing).
- Self-updating is a convenience, not a guarantee. For anything you will tell a client, follow the link in `sources.md` and read the primary source.

## Contributing

Contributions are welcome through issues and pull requests on the repository host. Please keep these rules, which are the same ones the agents follow:

1. **A lesson must generalise.** Ask whether a stranger on a different project would hit the same problem. Use the entry shape at the top of each `LESSONS.md` (date, symptom, cause, rule).
2. **Cite a primary source with a date** for any claim about a search engine, browser, standard or vendor. Fetch the page, compare the wording, record the URL and the day in `references/sources.md`. Mark anything you could not verify as unverified.
3. **Keep rules short:** one or two lines plus the reason. Put long examples in `references/`.
4. **Append, do not rewrite, the lessons log.** Corrections are new entries that quote what they replace.
5. **Sanitise.** No client, company or person names, no real domains, internal paths, ticket numbers, e-mail addresses or keys. Use neutral examples ("an agency site", "a clinic site in Arabic and English").
6. **Update `CHANGELOG.md`** when a rule is added, changed or removed.

A quick self-check before you open a pull request:

```bash
grep -rniE "api[_-]?key|secret|password|token=|@[a-z0-9-]+\.[a-z]{2,}|[a-z]:\\\\|/home/|/Users/" .
```

Review every hit by eye: documentation words such as "token" and the example domain `example.com` are expected; anything that identifies a real person, business, machine or credential is not.

## Scope and limits

- These are working checklists, not guarantees of ranking, conversion or compliance. Search engines and browsers change; the dated sources and the freshness check exist for that reason.
- Nothing here is legal advice. Privacy, advertising and health-content rules differ by country; the skills flag where law applies and tell you to verify the rules of each audience country.
- Rules marked `[C]` in the SEO skill are conventions that have worked in practice, not documented search-engine requirements.

## Licence

MIT. See [LICENSE](LICENSE). Copyright the contributors.
