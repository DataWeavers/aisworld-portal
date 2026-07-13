# About ISC Board

## Who made this

Arvin Mesgari — a short line about who you are and why you built this
(e.g. an IS researcher/practitioner who wanted an easier way to scan the
AISWORLD mailing list). Add a contact link if you'd like.

## What it does

ISC Board is a read-only, unofficial companion to the AISWORLD mailing
list. AISWORLD is high-volume and hard to skim by email — this project
pulls the public feed, cleans it up, and presents it as a filterable
board so you can jump straight to the calls for papers, conferences, jobs,
or funding posts you care about.

It does not host the mailing list, send email, or let anyone post — it
only reads and re-presents what AISWORLD already publishes publicly.

## How the pipeline works

**1. Fetching.** A script (`scripts/fetch-feed.mjs`) pulls the AISWORLD
public RSS feed on a daily schedule (via a GitHub Action cron job) and
rebuilds `public/data.json`, which the site reads.

**2. Categorization ("Type" tags).** Each post's subject + description is
checked against a set of keyword/regex rules in `config/categories.json`
— e.g. "conference", "workshop", "tenure-track", "fellowship". Every rule
that matches applies, so a post can be tagged with more than one Type
(a call for a specific conference legitimately gets both). A post that
matches nothing falls back to "General Discussion." A post with a
recognized event acronym (see below) also automatically gets "Conference
& Workshop," even if the body text never uses that word.

**3. Topic and Event tags.** Beyond Type, posts are tagged with subject
matter (e.g. "AI & GenAI," "Cybersecurity & Privacy") and, when detectable,
the specific event/venue acronym (e.g. "AMCIS," "HICSS"). These three tag
groups — Type, Topic, Event — are independent facets: picking tags within
a group is an OR (any match), across groups is an AND (must satisfy each
group you've filtered on).

**4. De-duplication.** AISWORLD threads are often reposted verbatim over
several weeks. The subject line is normalized (stripping "Re:"/"Fwd:" and
punctuation) into a dedup key; posts sharing a key are merged into a
single card, with every occurrence's date kept (shown as "posted 3×") and
the link pointing at the most recent occurrence.

**5. Retention.** Threads whose most recent post is older than 90 days
are dropped from `data.json` to keep the board current (`RETENTION_DAYS`
in `scripts/fetch-feed.mjs`).

Tuning any of this — adding a keyword, changing retention, adjusting
dedup — only requires editing `config/categories.json` or the constants
at the top of `fetch-feed.mjs`; no rebuild step beyond the daily cron.

## Feedback

Use the "Send feedback" link on the site, or the contact form in the
footer — both go straight to me.

## Setup & deploy

- `public/` — the site itself; the only folder your host needs to serve.
- `scripts/fetch-feed.mjs` — pulls the feed, tags, dedupes, and rewrites `public/data.json`. Runs server-side via the included GitHub Action (`.github/workflows/update.yml`, daily cron).
- Deploy `public/` to GitHub Pages (or any static host); the Action commits fresh data and redeploys automatically.
- Local test: `npm install && npm run fetch && npx serve public`.
