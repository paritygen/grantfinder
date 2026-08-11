---
name: find-grants
description: >
  This skill should be used when the user asks to "find grants", "search for
  grant opportunities", "what grants should we apply for", "any new funding
  opportunities", "look for funders in [area]", or wants a ranked shortlist of
  grant opportunities matching their organization. Also use for recurring
  scans like "check for new grants this week".
metadata:
  version: "0.1.0"
---

# Grant Opportunity Discovery

Search public funding sources, screen against eligibility, score fit, and
produce a ranked shortlist written to `~/Documents/GrantFinder/opportunities/`.

## Before searching

Read `~/Documents/GrantFinder/org-profile/organization.md` and the program index. If the profile
does not exist, invoke `grant-profile` first — searching without a profile
produces generic results that waste the user's review time.

Confirm the search frame with the user if it is not already clear:

- **Program or need** being funded (specific project vs. general operating)
- **Amount** sought, and the floor below which applying is not worth the effort
- **Timeline** — when the money is needed, which sets the deadline window
- **Geography** — service area, which gates most place-based funders

## Priority watchlist — check first, every time

If `~/Documents/GrantFinder/sources.md` exists, read it before running any open
search. It is a user-curated list of funders and lead sources to check on every
scan. Check every source in it, follow the per-source instructions there, and
open each source live to verify current status — deadlines and open/closed
windows change without notice, so never rely on what a prior scan recorded.

Some watchlist entries are marked as lead sources rather than funders — a blog
or article listing grants. Do not report those as opportunities to apply to;
mine them for the funders they name and evaluate each named funder on its own.

After checking a source, update its **Last checked** date in `sources.md` and
report only what is new or changed since the previous date.

The watchlist supplements the open search below; it does not replace it.

## Where to search

Search these directly with web search and fetch. Run several searches in
parallel rather than sequentially.

| Source | Covers | Access |
|---|---|---|
| Grants.gov | US federal | Free, public. Search by CFDA/category/eligibility |
| SAM.gov | Federal assistance listings | Free, public |
| State & city grant portals | Government, place-based | Free, varies by state |
| Foundation websites | Private/family/corporate | Free but unstructured |
| 990-PF filings (ProPublica Nonprofit Explorer) | Who a foundation actually funded, and how much | Free, public |
| Community foundation sites | Regional | Free |
| Corporate giving pages | Corporate | Free |

**Paywalled sources are not accessible.** Candid / Foundation Directory Online,
Instrumentl, and GrantStation require paid logins this plugin cannot use. If the
user has a subscription, they can export results and drop the file in
`~/Documents/GrantFinder/opportunities/` — this skill will read and score it. Say so rather than
implying full database coverage.

990-PF filings are the highest-value free source for private foundations: they
list actual grants paid, to whom, and how much. Use them to check whether a
foundation really funds organizations like this one, at what size, before
recommending it.

## Screening: eligibility gates first

Before scoring anything, check hard disqualifiers. A disqualified opportunity is
reported as disqualified — never scored and ranked as if it were viable.

- Legal status (501(c)(3) determination required?)
- Geographic restriction — funder's service area vs. the org's
- Organization budget floor or ceiling
- Organization age (many funders require 2–3 years of operating history)
- Invitation-only / no unsolicited proposals — very common for family foundations
- Required match or cost share the org cannot meet
- Audit requirement above the org's current financial reporting
- Prior-grantee restrictions, or a cap on consecutive years of funding

Report each disqualifier with the specific criterion and the org fact that
conflicts with it, so the user can verify or override.

## Scoring fit

Score only opportunities that pass every gate. 100 points across six dimensions —
the full rubric with anchor definitions is in `references/scoring-rubric.md`.

| Dimension | Points |
|---|---|
| Mission alignment | 30 |
| Program fit | 25 |
| Funding size fit | 15 |
| Geographic fit | 10 |
| Capacity to comply | 10 |
| Funder relationship & history | 10 |

Bands: **80+** strong, apply. **65–79** viable, worth a conversation with the
program officer first. **50–64** weak, only if capacity is idle. **<50** skip.

Every score needs a one-line justification citing the specific evidence. A score
without evidence is a guess presented as analysis — do not produce one. Where
evidence is thin, say the confidence is low rather than inflating the number.

## Output

Write one file per opportunity to `~/Documents/GrantFinder/opportunities/` using
`${CLAUDE_PLUGIN_ROOT}/templates/opportunities/opportunity.md`.

Name the file by **stable identity**, not by date: `funder-slug--program-slug.md`
(for example `apa-foundation--moore-equity-community.md`). The identity is the
funder plus the specific program, and it never changes between scans — this is
what makes deduplication work. Do not put the discovery date or the deadline in
the filename; those live inside the file and change over time.

Then present a ranked table in the response. Every row links directly to the
grant:

| Score | Funder | Program | Amount | Deadline | Effort | Link |
|---|---|---|---|---|---|---|

The **Link** cell is a clickable markdown link to the specific opportunity —
`[Apply](https://…)` or `[Guidelines](https://…)`. This is required on every
row; a result the user cannot act on with one click is half-finished.

Link quality rules:

- **Deep link, not homepage.** Link to the specific grant, NOFO, or application
  page — not the funder's front door. `grants.gov/search-results-detail/<id>`,
  not `grants.gov`. If the funder only exposes a general grants page and no
  per-opportunity URL exists, link that page and say so in the Note.
- **The URL must be one you actually retrieved.** Never construct or guess a
  link. If a search returned a result, link the URL it returned. If no working
  URL was found, write "no direct link — [how to reach it]" rather than
  inventing a plausible one. A fabricated link that 404s costs the user a
  deadline.
- **Prefer the application entry point.** When both an overview page and an
  apply/portal page exist, link the one that moves the user toward applying, and
  keep the overview URL in the opportunity file.

Record the same URL in the opportunity file's **Source URL**, and record the
application/portal URL separately if it differs. The file holds both; the table
shows the one that gets the user moving.

Sort by score, but flag anything with a deadline inside 21 days regardless of
rank — a strong fit that cannot be assembled in time is not actionable.

Include a short **Disqualified** section below the table. Seeing what was ruled
out and why is how the user catches a wrong assumption in the profile.

## Deduplication — surface each grant once

The user does not want to see the same opportunity scan after scan. Enforce this
as a hard gate, not a preference.

**Build the seen-set before searching.** List `~/Documents/GrantFinder/opportunities/`
and read each file's identity (`funder-slug--program-slug`) and `Status`. This
is the set of opportunities already surfaced. Do this first, every scan.

**Check every candidate against it.** For each opportunity a search turns up,
derive its identity the same way — funder plus program. If that identity already
has a file, it is not new:

- Do **not** re-file it and do **not** put it in the "new opportunities" table.
- Update the existing file's **Last checked** date, and its deadline or
  open/closed status if those changed.
- If anything material changed, list it under a short **Updated** heading below
  the new-opportunities table — one line each. If nothing changed, say nothing
  about it at all.

**Only genuinely new identities go in the main table.** "New" means an identity
with no existing file. That is the answer to "show me new ones."

**Suppress dismissed opportunities.** If a file's `Status` is `Dismissed` (the
user rejected it) or `Skip`, never re-surface it, even as an update. It stays on
disk as the record of why it was ruled out. The one exception: a private
foundation the user dismissed that has since opened a *different* program is a
new identity, and is allowed through.

**A new cycle is an update, not a new grant.** When an annual program the user
has already seen opens its next round, that is the same identity. Update the
existing file's deadline and note it under **Updated** — do not present it as a
new discovery. The user recognizes the funder; what they need is the fresh
deadline, not a re-introduction.

Edge cases to get right:

- **Same funder, different program** → different identity → new. A funder can
  legitimately appear more than once.
- **Slug drift** — if a search phrases a funder's name differently than an
  existing file (e.g. "APA Foundation" vs "American Psychiatric Association
  Foundation"), match on the funder and program, not on an exact string, so a
  wording difference does not resurface a known grant under a second file. When
  in doubt, treat it as the existing identity and update, rather than creating a
  near-duplicate.

## Recurring scans

For "check for new grants this week", run the deduplication gate above and lead
with the new-opportunities table. If nothing is new, say so plainly — "no new
opportunities since [date]" is a complete and useful answer. Do not pad the
response by re-listing grants the user has already reviewed.

## Feedback

When the user says an opportunity is a bad match, record why in
`~/Documents/GrantFinder/org-profile/organization.md` under `## Search preferences` — for example
"skip opportunities requiring a 1:1 cash match" or "no federal pass-through
funding, reporting burden too high". Read that section on every subsequent
search. This is an explicit written preference list, not a trained model; it
improves results only because it is read back in, so keep it short and current.
