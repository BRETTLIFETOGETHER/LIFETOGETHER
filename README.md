# 40daycampaigns.com — v2 Platform Build (17,443-row master)

A complete, self-contained static site: **16,379 browsable campaigns & studies** plus **940 nested
Red Letter sessions** (17,319 active items), built entirely from
`Updated_40_Day_Campaign_Master__4_.xlsx` — every catalog entry traces to a Master ID; nothing
was invented. English at the root, Spanish under `/es/`, Portuguese under `/pt/`.

## Run it
No build step, no server, no network. Open `index.html` from this folder, or upload the folder
as-is to any static host (Netlify, Vercel, S3+CloudFront, cPanel). All fonts are base64-embedded;
there are zero external requests. Data ships as `.js` files so the site also works from `file://`.

## What's inside
- `*.html` — 24 pages ×3 languages (72 files): home, browse, channel/theme/campaign (parameterized),
  finder, builder (three tabs: Campaign · Sermon · Small Group), formats, included, pricing, how-it-works, for-pastors, for-churches, seasonal,
  about, case-studies, faq, cart, checkout, confirmation, account, signin/signup, contact.
- `assets/css` — design system (`site.css`) + embedded fonts (`fonts.css`: Hanken Grotesk,
  Spectral, Archivo — subset for EN/ES/PT).
- `assets/js` — `covers.js` (deterministic SVG cover engine, gold reserved for flagship),
  `app.js` (data layer, cart, pricing), `browse.js` (virtualized 16k-row catalog, inverted-index
  search, faceted filters, URL state), `campaign.js` (detail pages: daily arc, sermon builds,
  sample-day print/Word/Canva exports), `flows.js` (finder, builder, cart→checkout→confirmation,
  account).
- `data/` — `index.js` (compact catalog, 2.3 MB), 23 per-channel shards loaded on demand,
  `scripture.js` (verified reference pools + word banks), `meta.js`, plus `.json` twins of everything.
- `i18n/` — the full string tables per language.
- `build/` — the reproducible pipeline (see below).
- `VALIDATION.txt` — every gate, pass/fail, with counts.

## Data provenance
17,443 source rows → **excluded 124**: 81 third-party Market Benchmark products (reference-only per
the master's Conflict Flags), 36 rows marked "Do not build now", 7 structural DOCX headers.
940 "Life of Christ Supporting Content" rows are **nested** under their 150 Red Letter parent
series (visible on those campaign pages), leaving 16,379 top-level entries. The 304 rows flagged
"Do Not Send to AI Yet" are included as browsable listings only — the flag governs AI content
generation, which was not applied to them. Twenty source rows echo third-party program names
("Financial Peace", "Experiencing God", …); they are retained per the master's own Build Decisions
and listed in VALIDATION.txt for rename review.

## Taxonomy
23 channels derived from the real category structure (the 15-channel spec was a floor, not a
ceiling): the master's own families demanded Life of Christ & Red Letter, Family Legacy &
Generations, Work & Marketplace, Church Vision & Values, Ministries & Nonprofits, Seasons of Life,
Health/Healing & Care, and Bible & Scripture Studies as first-class channels, plus the two
validated white-space channels — **Doubt & Honest Faith** and **Digital Discernment & Faith in
the Age of AI** — marked NEW on the homepage. 1,742 themes sit beneath them. Audience is an
affinity filter, never a channel.

## The devotional arc (Phase 5 rebuild)
Engine days are no longer interchangeable. Every day-campaign now moves through five
phases — Invitation, Foundations, Practice, Perseverance, Commissioning. Day one is a real
welcome that names the campaign and sets the ten-minute habit; Sundays prepare people for
that weekend's actual message instead of assigning a solo reading; the final day
commissions, sending people back to the sentence they wrote on day one. Campaign titles are
woven into the text itself, opening lines vary by phase, and the generated prose obeys the
Lifetogether voice rules (no "journey", "unpack", "lean into", "season of life", or "do
life together" in engine output — product format names excepted).

Honest limitation, stated plainly: at 17,000-campaign scale this engine is a phased,
title-aware scaffold, not hand authorship. The quality path per campaign is the Claude
enhance step and the Complete Build tab, and every purchased document ships as editable
Word source.

## The Discernment Guide (finder)
The campaign finder is no longer a seven-question filter; it is Brett's assessment
pattern applied to the local church — a ten-minute instrument: six dimensions (Word &
Worship, Belonging, Whole Hearts, Open Hands, Home & Legacy, Sent), four congregation
statements each in the Purpose Driven cadence, scored out of 120 with three bands per
dimension; then the launch window, three honest facts about the church, up to three felt
needs, and one discernment sentence the pastor writes — echoed back on the results page
as the campaign's job description. Low dimensions and felt needs weight the 23 channels;
campaign history and groups culture set the recommended format; results render the scored
profile with bars and band copy, four matched campaigns with reason chips, a flagship
wildcard, and a print-ready profile. Content lives in build/finder_strings.py (fully
trilingual — the old finder's buttons were English-only on ES/PT pages; fixed), the
engine in assets/js/finder.js.

## The Gold Edition (design system)
The site now runs the dark luxury language supplied as the FLBD advisor-deck reference:
near-black ground (#0B0A10) with faint gold and slate radial glows, hairline borders,
Playfair Display as the display voice (a true italic variable face, 400-700, was added to
the self-hosted font set — see build/fonts.py), micro-labels in 8px letterspaced caps with
a leading rule, per-channel accent colors on every catalog card's top border and title,
outline-gold buttons that fill on hover, an italic serif hero with a five-cell stats
strip, and the 30-day guarantee set as a Playfair pull-quote. Architecture note: the
entire conversion rides on semantic tokens — original component rules were kept and the
palette inverted at :root, with a component voice layer appended at the end of
assets/css/site.css (search "THE GOLD EDITION"). One deliberate exception to the dark
theme: the sample-day reader (#daybody) stays a cream page inside the dark case, because
long devotional prose belongs on paper — the heirloom inside the jeweler's box.

## Corpus content quality (Phase 6)
No human can read a 252-million-word corpus, so quality was engineered in two layers.
Layer one: every atomic building block was hand-reviewed — all 23 channels' tensions,
practices, images, and sentence patterns were rewritten by hand, and every promise the
engine can make is now a **verse-bound pair**: a faithful paraphrase permanently welded to
the exact reference it paraphrases (207 pairs, each verified against the canonical verse
table, chapter and verse bounds included). A devotional's TRUTH line can only cite the
verse its own claim paraphrases, so misattributing Scripture is impossible by
construction — this replaced the old design, which paired references and promises
independently and could attribute one book's words to another.

Layer two: every assembled output is machine-verified. `build/corpus_audit.js` generates
every day of every format of every campaign plus all sermon and session builds —
1,357,200 days, 84,229 sermons, 84,229 sessions, 252,409,347 words — and checks each for
unfilled slots, undefined leaks, banned voice words, spacing and punctuation faults,
verb-in-noun-slot grammar breaks, reference format and canonicity, citation binding,
duplicate days, and Welcome/Sunday/commissioning structure. The current run reports zero
defects and zero duplicate days within any campaign (per-campaign dealt decks guarantee
no opener, tension, promise, practice, image, question, or prayer repeats until its whole
pool is exhausted). The audit runs inside `build/validate.py` as a permanent gate, so a
regression can never ship silently.

Campaign titles are treated as publisher data and woven verbatim; five master-spreadsheet
rows carry placeholder titles that should be renamed at the source (listed in
VALIDATION.txt under "Source-data quality").

## SEO surface
All 383 Grade-AA flagship campaigns are prerendered as real static pages under `/c/` with
baked titles, meta descriptions, canonical URLs, and Product JSON-LD — hydrating through
the same campaign.js (`window.CAMPAIGN_ID`). `sitemap.xml` lists 451 URLs (core pages ×3
languages, all 23 channels, every /c/ page); `robots.txt` points to it; every core page
carries canonical + en/es/pt hreflang alternates; the homepage carries Organization
JSON-LD. The remaining catalog stays client-rendered behind those crawlable listing pages.

## Guarantee
A 30-day full-refund guarantee appears on the pricing page, the homepage pricing section,
and beside the checkout summary. **This is editable business copy, not a legal document** —
adjust the wording in `build/strings.py` (key: `guarantee`, three languages) before launch
if your terms differ.

## Format availability (Phase 2 audit)
Every campaign that can run as a 40, 30, 21, or 7-day journey now offers all four —
13,373 rows (81.6%). Formats lock only where identity forbids it: the title itself names a
length ("30 Days to Easter" stays 30; 437 forty-only, 98 thirty-only, 84 twenty-one-only,
39 seven-only), Catalytic Sundays stay one day (645), one-year initiatives stay one year (41),
and non-campaign product types — webinars, workshops, assessments, toolkits — carry a
Ministry Resource format (1,632) with its own detail layout instead of a fictional day arc.

## Content engine
`assets/js/engine.js` fully writes every unit of every campaign on demand: each devotional
day (opening, Scripture-anchored truth, application turn, concrete step, reflection question,
and prayer), complete sermon builds (big idea, three movements with texts and coaching
sentences, illustration prompts, landing and response), and host-ready group sessions.
Generation is deterministic — seeded by the campaign's fingerprint — so a shared URL always
shows identical text, and every campaign page offers one-click .doc downloads of the complete
devotional, the full sermon set, and the group guide, generated client-side.

## Claude connection
`assets/js/ai.js` wires the Builder to claude-sonnet-4-6 through the Anthropic Messages API.
All three Builder tools — custom Campaign, build-your-own Sermon, build-your-own Small Group —
render an instant engine draft, then offer "Enhance with Claude," which rewrites the draft in
the pastor's stated voice. Inside Claude-hosted contexts the call works as-is with no key;
anywhere else it fails gracefully back to the engine draft with a clear toast. The file's
header marks the SELF-HOSTING SEAM: route the call through a small backend proxy holding your
Anthropic API key — never ship a key to the browser.

## The Campaign Engine (title in, complete campaign out)
The Builder's **Complete Build** tab takes a title and subtitle and produces a full
publishable campaign to the LifeTogether spec. The week count is derived from the subtitle
itself and never standardized: "An 8-Week Study" yields 8 sessions and 40 devotional days
(weeks x 5, Monday–Friday, so groups meet having read the same five); a 4-week study yields
20. If the subtitle names a fixed set whose count matches the weeks — the eight beatitudes,
the seven last words — sessions follow that set one per week.

It builds sessions (three commentary paragraphs, five formation questions each with a note
on what happens in the room, a dated application, a first-person prayer, two stall notes),
every devotional day, a sermon outline per week with a platform title distinct from the
session title, all eight kit assets for **every** week, and the six completion systems —
testimony capture, the 90-day callback, host recruitment, measurement, alternate lengths,
and accessibility.

Because the full output is roughly 20,000 tokens — past a single response and far past
Netlify's function timeout — `assets/js/spec.js` plans the work as many small passes (an
8-week campaign is 34) and runs them three at a time with a live progress bar and one retry
each. A pass that still fails leaves its gap named in the verification box instead of
discarding the rest. Everything then goes through `verify()`, which re-counts sessions,
days, sermons, and all eight kit assets per week, checks for banned voice words, and flags
sermon titles that repeat their session title. Exports: full `.doc` and raw JSON in the
spec's schema.

**Batch mode** accepts a list — `Title | Subtitle | Text`, one per line — derives each
title's week count independently, and stops after five per pass by design.

## Stripe seam
`assets/js/flows.js`, in the checkout block, contains a marked comment titled
**STRIPE INTEGRATION SEAM** with the exact fetch call to swap in. Until it is wired, checkout
validates, stores the order locally, unlocks the four starter downloads (real, editable .doc files
generated client-side), and routes to confirmation — so the flow is demonstrable end-to-end today.

## Scripture standard
All references are named to the NIV standard and validated against a canonical verse-count table
at build time (461 distinct refs; zero invalid). Where verse text is embedded in generated
documents, wording stays paraphrase/KJV-safe pending the Biblica NIV license flagged for platform
scale. 2,352 rows sit in themes larger than their channel's verified pool, so backbone references
repeat there in spaced rotation — disclosed in VALIDATION.txt rather than papered over with
invented citations.

## Languages
ES and PT are full editions: navigation, marketing pages, channel and format names, commerce,
and legal strings are translated in ministry register. **Campaign titles remain English** —
translating 16,379 titles is an editorial ministry-copy pass, not a build step; the string tables
in `/i18n` are ready to receive them.

## Regenerating from the master
From `build/` in order: `generate_data.py` (taxonomy, exclusions, nesting, attributes) →
`emit_site_data.py` (Scripture assignment, shards, index) → `fonts.py` (fetch + subset + embed) →
`pages.py` (all 72 pages) → `node smoke.js` → `validate.py` (writes VALIDATION.txt, exits non-zero
on any hard failure). Requires Python 3 with pandas, fonttools, brotli, beautifulsoup4, and Node
with jsdom. Note: the build container's network allowlist blocks Chromium downloads, so runtime
verification uses jsdom; run a Playwright crawl in CI once hosted.
