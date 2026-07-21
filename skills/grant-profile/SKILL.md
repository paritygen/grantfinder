---
name: grant-profile
description: >
  This skill should be used when the user asks to "set up my grant profile",
  "update our organizational information", "add a program description",
  "what do we have on file", "check our documents", "update our financials",
  or otherwise wants to create, review, or maintain the reusable organizational
  data that grant applications draw on. Also use it whenever another grant
  skill needs profile data that does not yet exist.
metadata:
  version: "0.1.0"
---

# Organizational Profile & Data Repository

Maintain the single source of truth that every other grant skill reads from. All
data lives in plain markdown under the workspace so the user can read, edit, and
version it without this plugin.

## Workspace layout

The workspace is always `~/Documents/GrantFinder/` — a fixed absolute location in
the user's home Documents folder. It does **not** move with whichever folder is
open in the session.

This is deliberate. A workspace resolved relative to the current folder produces
several partial copies over time, and the outcome analysis then reads whichever
fragment happens to be in front of it without any sign that data is missing.

Create the directory if it does not exist. Never create a second grant workspace
somewhere else, and never write grant data into the folder that happens to be
open — if the user asks for that, explain the fragmentation risk first.

## Grant facts go in the workspace, not the memory store

When the user says "remember that our EIN is …", "remember we don't apply to
federal funders", or anything else that is a durable grant fact, write it to the
appropriate file in `~/Documents/GrantFinder/` — not to the session memory
store.

Session memory is keyed to the folder that is open, so a fact saved there
disappears the moment the user works from a different folder, and the profile
that applications are built from silently loses it. It is also invisible to the
user as a document: it cannot be reviewed before a submission, handed to a
colleague, or checked against the 990.

Route by type:

| The user says | Goes to |
|---|---|
| A fact about the organization | `org-profile/organization.md` |
| A budget or financial figure | `org-profile/financials.md` |
| Board or staff information | `org-profile/people.md` |
| Something about a program | `org-profile/programs/<program>.md` |
| Reusable prose for applications | `org-profile/narratives/<question>.md` |
| A funder preference or exclusion | `org-profile/organization.md` → `## Search preferences` |
| A result, or a funder's stated reason | `outcomes.md` |

Confirm where it was written, so the user knows the fact is in a file they can
open rather than somewhere they cannot inspect.

```
~/Documents/GrantFinder/
├── org-profile/
│   ├── organization.md      # identity, mission, legal status, contacts
│   ├── financials.md        # budget, revenue mix, audit status, fiscal year
│   ├── people.md            # board, key staff, org chart notes
│   ├── documents.md         # registry of supporting docs + expiry dates
│   ├── programs/            # one file per program
│   └── narratives/          # reusable answers, one file per question type
├── opportunities/           # one file per grant opportunity found
├── applications/            # one folder per application in progress
└── outcomes.md              # ledger of submitted applications and results
```

Templates for every file are in `${CLAUDE_PLUGIN_ROOT}/templates/`. Copy the
relevant template rather than inventing structure.

## Setting up a new profile

Run this when `~/Documents/GrantFinder/org-profile/` does not exist or is mostly empty.

1. Check whether the user already has source material — a recent grant
   application, a 990, an annual report, a capability statement. Reading one
   existing application fills most fields at once. Ask before assuming there is
   nothing to work from.
2. Copy templates into `~/Documents/GrantFinder/org-profile/`.
3. Fill what the source material supports. Mark everything else `TODO:` with a
   note on who can supply it. Do not invent values.
4. Report back what is filled, what is missing, and which gaps will block
   applications (EIN, 501(c)(3) determination letter, and current-year budget
   block nearly everything).

## Recording facts

Every fact that varies over time carries a `_last verified_` date. Every
fact a funder will check carries a source.

```markdown
- **EIN:** 12-3456789 _(last verified 2026-03-14 · source: IRS determination letter)_
- **Annual operating budget:** $2,450,000 _(last verified 2026-01-08 · source: FY26 board-approved budget)_
```

When updating a value that already exists, do not overwrite silently. Append the
prior value to a `## Change log` section at the bottom of the file with the date
and what changed. This is the version history — there is no database behind it.

## The narrative library

`narratives/` holds reusable prose keyed by the question it answers, not by the
funder that asked. Typical files: `mission-statement.md`,
`organizational-history.md`, `need-statement.md`, `evaluation-approach.md`,
`dei-commitment.md`, `sustainability-plan.md`.

Each file holds several length variants of the same content, because funders
impose different word limits:

```markdown
## 50 words
...
## 150 words
...
## 500 words
...
```

Keep the variants saying the same thing. If a funder-specific spin is needed,
that belongs in the application folder, not here — the library stays generic.

## Document expiry

`documents.md` tracks supporting documents and when they go stale. Audited
financials, insurance certificates, state charitable-solicitation registrations,
and board rosters all expire.

There is no background process to notify anyone. Instead, check expiry whenever
this skill or `find-grants` runs, and surface anything expiring within 60 days
in the response. State this limitation plainly if the user expects automatic
alerts.

## Reading the profile for other skills

When another skill needs profile data, read only the files it needs — do not
load the whole repository. `find-grants` needs `organization.md` and the program
index; `draft-application` needs narratives and financials.

If required data is missing, say which file and field is missing rather than
guessing or writing placeholder prose into an application.

## Reference

- `references/profile-fields.md` — the full field list per file, with notes on
  which funders ask for what.
