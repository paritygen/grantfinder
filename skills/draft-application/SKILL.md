---
name: draft-application
description: >
  This skill should be used when the user asks to "draft a grant application",
  "help me answer these grant questions", "fill out this application", "write
  the narrative for [funder]", "we got the application questions", or shares a
  set of grant application questions to be answered from stored organizational
  data.
metadata:
  version: "0.1.0"
---

# Application Drafting & Content Assembly

Assemble an application from the organizational repository, drafting only what
the repository cannot supply directly.

## Set up the application

Create `grants/applications/YYYY-funder-slug/` containing:

```
questions.md    # the funder's questions, verbatim, with limits and requirements
draft.md        # working answers
sources.md      # which repository file each answer drew from
checklist.md    # attachments, signatures, portal steps, submission mechanics
```

Copy the funder's questions in exactly as written, including word and character
limits. Paraphrasing a question loses the constraint that shapes the answer.

## Classify each question

Sort every question into one of three types before drafting anything.

**Direct lookup** — the answer is a stored fact. EIN, address, budget, staff
count, fiscal year end, board chair. Pull from `grants/org-profile/` and cite
the file. If a fact is missing or its `_last verified_` date is over a year old,
flag it rather than using it silently.

**Library reuse** — the answer exists in `grants/org-profile/narratives/` and
needs trimming to the word limit and light tailoring to the funder's language.

**Net-new** — project-specific content that does not exist yet: this project's
activities, budget, timeline, outcomes, evaluation plan. These need the user's
input. Draft a structured skeleton with explicit gaps rather than inventing
plausible-sounding specifics.

Report the classification counts before drafting, so the user knows where their
time will go.

## Drafting rules

**Never invent a number.** Not a beneficiary count, not an outcome percentage,
not a budget line, not a year founded. Every number in a grant application is a
representation to a funder and may be audited. If a number is needed and not on
file, write `[NEED: number of clients served FY25]` and list it in the gaps
section.

**Answer the question that was asked.** Grant reviewers score against a rubric
tied to the question. A beautifully written answer to an adjacent question
scores zero. When a question has multiple parts, address each part explicitly.

**Match the funder's vocabulary.** If they say "clients", do not say
"participants". If they say "evidence-based", use their framing of it. This is
not keyword stuffing — reviewers scan for their own concepts.

**Respect limits precisely.** Count words against the stated limit and report
the count. Portals commonly truncate at the character limit without warning,
cutting an answer mid-sentence.

**Cite sources inline while drafting**, recorded in `sources.md`:
`[from: org-profile/narratives/need-statement.md]`. The user needs to know what
was retrieved versus what was generated, to know what to verify.

## Present for review

Show drafted answers with the question, the limit, the current word count, the
source, and any gaps. Keep the funder's question visible above each answer.

Mark each answer's state:

- **Ready** — assembled from verified repository content, within limits.
- **Review** — drafted, but needs the user's judgment on framing or emphasis.
- **Blocked** — cannot be completed without information only the user has.

Lead the response with the Blocked items. Those set the timeline.

## Compliance check before export

Run before the user submits, and report as a checklist:

- Every required question answered
- Every answer within its word and character limit
- No `[NEED: ...]` placeholders remaining
- No `TODO:` markers carried in from the profile
- Every attachment in the checklist accounted for
- Budget totals add up, and match the amount stated in the narrative
- Requested amount within the funder's stated range
- Org legal name, EIN, and address match the IRS determination letter exactly
- Signatures and dates identified, with who must sign

Report failures as failures. Do not describe an application with unresolved
gaps as complete.

## Update the library

When an answer is written that will be reused — a stronger need statement, a
clearer program description — offer to add it to
`grants/org-profile/narratives/`. This is how the repository improves. Ask
first; not every funder-specific answer belongs in the generic library.

## Reference

- `references/question-patterns.md` — common grant question types and how to
  approach each.
