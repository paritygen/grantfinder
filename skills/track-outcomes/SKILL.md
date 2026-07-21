---
name: track-outcomes
description: >
  This skill should be used when the user asks to "log a grant outcome", "we got
  the grant", "we were rejected", "track this application", "what's our win
  rate", "what's working in our grant applications", "which funders should we
  focus on", or wants analysis of past grant results to guide future strategy.
metadata:
  version: "0.1.0"
---

# Outcome Tracking & Pattern Analysis

Maintain the ledger of submitted applications and their results, and draw
conclusions from it only when the data supports them.

## The ledger

`grants/outcomes.md` holds one row per submitted application. Create it from
`${CLAUDE_PLUGIN_ROOT}/templates/outcomes.md` if absent.

Record at submission, then update at decision:

| Field | Notes |
|---|---|
| Funder | Organization name |
| Funder type | Federal / State / Local govt / Private foundation / Community foundation / Corporate |
| Program area | Match the org's own program names |
| Amount requested | |
| Amount awarded | Blank until decided; partial awards are common and informative |
| Submitted | Date |
| Decided | Date |
| Result | Awarded / Declined / Partial / Withdrawn / Pending |
| Fit score | The score given before applying — needed to check whether scoring works |
| Effort | Light / Moderate / Heavy / Very heavy |
| Reason given | The funder's stated reason, verbatim if available |
| Notes | Anything that would change a future decision |

Log declines with the same care as awards. A ledger of only wins cannot show a
pattern.

**Ask the funder why.** Most program officers will explain a decline if asked
directly, and that reason is worth more than any inference from the ledger.
Prompt the user to ask, and record the answer in `Reason given`.

## Analysis — and when to refuse it

Small samples produce confident nonsense. Grant outcomes arrive slowly; a
typical organization has a handful per year. Apply these thresholds and state
them in the output.

| Decided applications | What to report |
|---|---|
| Under 10 | Individual outcomes and stated reasons only. No rates, no patterns. |
| 10–29 | Overall rate with its range. Segment breakdowns as raw counts, never percentages. |
| 30+ | Segment rates, with the count shown alongside every rate. |

Within a segment, never state a rate on fewer than 8 decided applications. "2 of
3 federal applications succeeded" is a fact; "67% federal success rate" is a
claim the data cannot carry.

Always show the denominator: "4 of 11 (36%)", not "36%".

The user may ask for a pattern the data cannot support. Give them the raw counts
and say plainly why a rate would mislead. That is more useful than a number they
might act on.

## Reporting

Structure the analysis as:

1. **Where things stand** — pending applications, total requested, decisions
   expected, and by when.
2. **Results** — counts and, where the threshold allows, rates by funder type,
   grant size, and program area.
3. **Stated reasons** — group the funders' own decline reasons. This is the
   most actionable section and needs no statistical threshold, because it is
   direct evidence rather than inference.
4. **Observations** — patterns worth noting, each with its supporting counts and
   an explicit confidence level.
5. **Suggestions** — what to do differently, each traced to a specific
   observation.

Mark every observation:

- **Supported** — meets the sample threshold, consistent across the ledger.
- **Suggestive** — a visible pattern below threshold; worth watching.
- **Anecdotal** — one or two cases; recorded, not concluded from.

Never present a suggestive or anecdotal pattern as a finding.

## Checking the fit scores

Compare the fit score assigned before applying against the outcome. If declined
applications routinely scored above 80, the scoring is miscalibrated — surface
that. This check matters more than the win rate, because it corrects the tool
the user relies on to choose where to apply.

Note the selection effect: the org mostly applies to high-scoring opportunities,
so there is little low-score data to compare against. Mention this rather than
reading the comparison as clean evidence.

## Assessing a planned application

When the user asks whether a planned application is likely to succeed, do not
produce a percentage. A probability implies a model that does not exist here.

Instead give:

- **Comparable applications** from the ledger — similar funder type, size, and
  program — with what happened to each.
- **Factors that align** with past awards.
- **Factors that align** with past declines.
- **A judgment**: stronger, comparable, or weaker than past applications, with
  the reasoning.
- **What would most improve it**, tied to specific stated decline reasons.

If there are no comparable applications on file, say so. Grant outcomes turn on
funder priorities, board dynamics, and timing that are invisible from the
outside — a specific number would be false precision, and the user may make real
resource decisions on it.

## Effort returned

Track amount awarded against effort spent. A heavy application that wins
$15,000 may be a worse use of the same week than three light applications. Once
the ledger has enough decided entries, report awarded dollars by effort tier —
this often changes strategy more than win rate does.
