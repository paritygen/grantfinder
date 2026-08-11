# GrantFinder

A Claude Cowork plugin for finding, evaluating, and applying for grants. All
data is stored as plain markdown in `~/Documents/GrantFinder/` — readable,
editable, and version-controllable without this plugin.

## Skills

| Skill | Use it when |
|---|---|
| **grant-profile** | Setting up or updating organizational, program, and financial information |
| **find-grants** | Searching for opportunities and getting a ranked shortlist |
| **evaluate-grant** | Deep-diving one specific opportunity for a go/no-go |
| **draft-application** | Answering a funder's application questions from stored data |
| **track-outcomes** | Logging results and analyzing what is working |

Skills trigger from natural language — "find grants for our literacy program",
"should we apply for this?", "we got the Hartwell grant". No commands to learn.

## Getting started

1. Install the plugin.
2. Say "set up my grant profile". The fastest path is to hand over a recent
   grant application, a 990, or an annual report — one document fills most
   fields at once.
3. Say "find grants for [program]".

## Where your data lives

```
~/Documents/GrantFinder/
├── org-profile/          # reusable organizational information
│   ├── organization.md
│   ├── financials.md
│   ├── people.md
│   ├── documents.md      # supporting docs and expiry dates
│   ├── programs/         # one file per program
│   └── narratives/       # reusable answers at several word lengths
├── opportunities/        # one file per opportunity, with fit score
├── applications/         # one folder per application in progress
└── outcomes.md           # ledger of submissions and results
```

Plus one optional file you create as needed:

```
~/Documents/GrantFinder/
└── sources.md            # priority watchlist — funders to check every scan
```

Templates for every file are in `templates/`.

This location is fixed. The plugin always reads and writes
`~/Documents/GrantFinder/`, regardless of which folder is open in the session —
so there is one outcomes ledger and one profile, not a partial copy per project
folder. It also keeps organizational financials out of any repository the
plugin itself is distributed from.

To move the workspace, change the path in the five `SKILL.md` files; it appears
near the top of each.

**Facts belong in these files, not in session memory.** When you say "remember
our EIN is…" or "remember we don't apply to federal funders," the plugin writes
it to the right profile file and tells you which one — never to the folder-keyed
session memory, where it would vanish when you open a different folder and be
invisible to the applications built from it. Everything the plugin remembers is a
file you can open and check before a submission.

## The priority watchlist

`sources.md` is a list of funders and lead sources to check on **every** scan,
on top of the plugin's open search. Say "always check [funder] for grants" and
it gets added; edit the file directly to add or remove sources.

Each entry can carry per-source instructions — how to check it, what it funds,
eligibility quirks. Some entries are marked as *lead sources* rather than
funders (for example a blog listing grants); the plugin mines those for the
funders they name instead of treating the article itself as an opportunity.

## How opportunities are scored

Eligibility gates are checked first — a disqualified opportunity is reported as
disqualified, never ranked as viable. Opportunities that pass are scored out of
100: mission alignment (30), program fit (25), funding size fit (15), geographic
fit (10), capacity to comply (10), funder relationship (10).

Every score carries evidence, an effort estimate, and a confidence level. A
high score with low confidence means "call the program officer", not "apply".

Full rubric: `skills/find-grants/references/scoring-rubric.md`.

## Results you can act on

**Every result links directly to the grant.** The ranked table ends in a Link
column with a clickable link on each row, pointing at the specific opportunity
or application page — not the funder's homepage. Links are only ever the ones
actually retrieved during the search; the plugin never constructs or guesses a
URL, so a link that would 404 at a deadline is written as "no direct link" with
the closest funder page instead.

**Each grant is surfaced once.** Opportunities are stored by a stable identity
(funder + program), not by date, so the same grant is not rediscovered scan
after scan. A recurring scan reports only genuinely new opportunities; a known
grant whose deadline or status changed appears under a short "Updated" list, and
anything you dismiss stays suppressed — kept on disk as the record of why it was
ruled out. "No new opportunities since [date]" is a complete answer.

## What this plugin does not do

Stated plainly so expectations match reality.

**No paid database access.** Candid / Foundation Directory Online, Instrumentl,
and GrantStation need paid logins the plugin cannot use. It searches free public
sources: Grants.gov, SAM.gov, state and city portals, funder websites,
community foundation sites, and 990-PF filings via ProPublica. If you have a
subscription, export results into `~/Documents/GrantFinder/opportunities/` and the plugin will
read and score them.

**No background monitoring.** Nothing runs on a schedule. Searches happen when
you ask, and document expiry is checked only when a skill runs. For recurring
scans, ask for one — "check for new grants this week" — and the plugin reports
only what has changed since the last check.

**No success probability scores.** `track-outcomes` will not produce a "68%
likely to win" number. Grant decisions turn on funder priorities, board
dynamics, and timing that are not visible from the outside, and no model here is
trained on them. Instead it gives comparable past applications, aligning and
conflicting factors, and a judgment of stronger / comparable / weaker with
reasoning.

**Statistical thresholds are enforced.** Under 10 decided applications: no rates
at all. Under 8 in a segment: raw counts only. Denominators always shown. Small
grant samples produce confident nonsense, and the plugin will tell you when the
data cannot support the pattern you asked for.

**No numbers are invented.** Beneficiary counts, outcome percentages, budget
figures — if it is not in your profile, drafts get `[NEED: ...]` rather than a
plausible-looking figure. Everything in a grant application is a representation
to a funder and may be audited.

## Customizing

The scoring rubric weights are in
`skills/find-grants/references/scoring-rubric.md` and can be edited directly —
an organization dependent on government funding might weight capacity higher, or
one with a national scope might drop geography.

Every skill and template is plain markdown. Edit them.

## Version

0.1.0
