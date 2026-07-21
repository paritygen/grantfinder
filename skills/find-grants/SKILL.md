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
`${CLAUDE_PLUGIN_ROOT}/templates/opportunities/opportunity.md`, named
`YYYY-MM-DD-funder-slug.md` by deadline.

Then present a ranked table in the response:

| Score | Funder | Program | Amount | Deadline | Effort | Note |
|---|---|---|---|---|---|---|

Sort by score, but flag anything with a deadline inside 21 days regardless of
rank — a strong fit that cannot be assembled in time is not actionable.

Include a short **Disqualified** section below the table. Seeing what was ruled
out and why is how the user catches a wrong assumption in the profile.

## Recurring scans

For "check for new grants this week", read the existing files in
`~/Documents/GrantFinder/opportunities/` first and report only what is new or changed. Do not
re-present opportunities the user has already reviewed. Note in each file the
date it was last checked.

## Feedback

When the user says an opportunity is a bad match, record why in
`~/Documents/GrantFinder/org-profile/organization.md` under `## Search preferences` — for example
"skip opportunities requiring a 1:1 cash match" or "no federal pass-through
funding, reporting burden too high". Read that section on every subsequent
search. This is an explicit written preference list, not a trained model; it
improves results only because it is read back in, so keep it short and current.
