---
name: evaluate-grant
description: >
  This skill should be used when the user asks to "evaluate this grant",
  "should we apply for this", "is this a good fit", "look into this funder",
  "check if we're eligible", or shares a specific grant opportunity, RFP, NOFO,
  or funder URL for assessment. Use for deep analysis of one opportunity, as
  opposed to find-grants which produces a broad shortlist.
metadata:
  version: "0.1.0"
---

# Evaluate a Single Opportunity

Produce a go / no-go recommendation on one opportunity, grounded in the funder's
actual giving behavior rather than their marketing language.

## Gather the source material

Work from primary sources. Do not evaluate from a summary or a listing site's
paraphrase — those omit exclusions that decide eligibility.

1. The full funder guidelines, RFP, or NOFO.
2. The funder's own site: about, priorities, grants list, FAQ, staff page.
3. The most recent 990-PF (private foundations) or 990 (public charities and
   operating foundations) via ProPublica Nonprofit Explorer. Read the grants
   paid schedule.
4. `~/Documents/GrantFinder/outcomes.md` for prior history with this funder.
5. `~/Documents/GrantFinder/org-profile/` for the facts to check eligibility against.

If the user supplied a PDF or a URL, read it in full before searching.

## What the 990 tells you

The single most useful step, and the one most often skipped.

- **Total giving** vs. the size of this opportunity — is this their whole budget
  or a rounding error?
- **Median grant size** from the grants paid schedule. Compare to the advertised
  range. The gap is usually large.
- **Grantee list** — are there organizations like this one? Same size, same
  field, same geography?
- **Repeat grantees** — a list dominated by the same names year over year means
  a closed circle. Score the relationship dimension accordingly and tell the
  user plainly.
- **Trustees and officers** — check against the org's board for an existing
  connection.

## Eligibility, checked one criterion at a time

Walk the guidelines and extract every requirement. For each, state the
requirement, the org's actual fact, and the verdict.

```markdown
| Requirement | Our status | Verdict |
|---|---|---|
| 501(c)(3) in good standing | Yes, determination letter 2019 | Pass |
| Serving Cuyahoga County | Yes, primary service area | Pass |
| Operating budget under $5M | $2.45M (FY26) | Pass |
| Audited financials, 2 years | Reviewed only, not audited | **Fail** |
| 1:1 cash match | Not currently secured | **At risk** |
```

Distinguish three verdicts: **Pass**, **Fail** (a hard stop), and **At risk**
(possible but requires action before the deadline). Never soften a Fail into an
At risk — a wasted application costs real staff time.

## Score and recommend

Apply the rubric in
`${CLAUDE_PLUGIN_ROOT}/skills/find-grants/references/scoring-rubric.md`. Include
the effort estimate and confidence level.

Then give a direct recommendation — **Apply**, **Apply if [condition]**,
**Contact the program officer first**, or **Skip** — with the reasoning in two
or three sentences. Lead with the recommendation; do not bury it under analysis.

"Contact the program officer first" is the right call more often than it gets
made: when fit is strong but the funder's practice is unclear, when the ask size
is uncertain, or when an At-risk criterion needs a ruling.

## Write it up

Save to `~/Documents/GrantFinder/opportunities/YYYY-MM-DD-funder-slug.md` using the template at
`${CLAUDE_PLUGIN_ROOT}/templates/opportunities/opportunity.md`. If the file
already exists, update it and note the re-evaluation date rather than replacing
the prior assessment — a changed verdict is worth seeing.

## Deadline reality check

Compare the deadline against the effort estimate and what the application
requires that the org does not yet have. Letters of support take a week to
collect. Audited financials take months. An SF-424 requires active SAM.gov
registration, which can take weeks to obtain or renew.

If the required lead time exceeds the time remaining, say so up front — that
fact outranks the fit score.
