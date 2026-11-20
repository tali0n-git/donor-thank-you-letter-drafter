# Donor Thank-You Letter Drafter

A draft-only automation that turns a donor spreadsheet row into a first-draft thank-you letter in a Google Doc, for a named person to check before anything reaches a donor. Built for the AI Tech Corps capstone (OptimaNova AI, Cohort 1).

> **Practice engagement.** The client, Linden Street Community Partners, is fictional and its staff were simulated. Every donor here is made up.

**Result in our demo:** drafting a letter went from about 12 minutes to about 2 to 3 minutes. See the caveats under [Before and after](#before-and-after).
**Demo video:** [LINK COMING SOON]

## Problem

Priya Raman, Development and Communications Manager, writes each donor thank-you by hand: about 12 minutes a letter, about 120 a month, two to three weeks behind. That is about 24 hours a month. These are her estimates from interviews.

We ran ten interviews across five staff, scored AI readiness at 38 out of 100 (Exploring, every score Directional), and ranked use cases. The letter helper ranked first.

## Build

```
New row in donor sheet -> count earlier gifts by email and date -> router:
  Returning donor  ("thank you for your continued support")
  Designated gift  ("thank you for your contribution to the [fund]")
  Regular gift     (standard thank-you)
-> Gemini drafts the letter -> Make saves it as a Google Doc
```

- **Tools:** Make, Google Sheets, Google Docs, Gemini API.
- **Routing is plain filtering.** The AI only writes the letter. First name and date are computed in Make, so the model never guesses them.
- **Files:** [prompts](prompts/donor_letter_prompts.md), [importable scenario](blueprint/donor_thank_you_drafter.blueprint.json), [made-up data](data/sample_donors_made_up.csv).

## Human check

- Nothing sends. The scenario has no email step, so a person contacts the donor.
- Priya checks each draft against its sheet row (name, amount, date, designation, voice), edits it, and sends it herself. The Executive Director is backup.
- Each draft ends with a "Check before sending" list. A row missing a name, amount or date returns `NEEDS INFO` and no letter.

## Test log

Made-up data only. Failures stay in the log.

| # | Input | Result | Change |
| --- | --- | --- | --- |
| 1 | Designated gift, Raymond Okoye, $250, Food pantry | Pass. Accurate, named the pantry | Rewrote base prompt for automation |
| 2 | Regular gift, Marisol Vega, $50 | Fail. Repeated "thank you" and "support" | Told Gemini not to repeat them |
| 3 | Rerun of 2 | Pass. Less repetitive | Added the rule to the NEVER list |
| 4 | Two-person name, "Maria and Jose Test-Garcia" | Fail. "NEEDS INFO: First name," no letter | Logged: handle multiple names |
| 5 | Rerun of 4 after the fix | [ADD RESULT] | [ADD CHANGE] |
| 6 | Fail-safe: amount blank | Pass. "NEEDS INFO: Gift amount" | None |
| 7 | Fail-safe: email and address missing | Fail. Letter written anyway | Logged: add checks. [UPDATE IF RERUN] |

## Before and after

| Measure | Before | After |
| --- | --- | --- |
| Time per letter | About 12 minutes by hand | 2 to 3 minutes to draft and check |
| Hours a month, about 120 letters | About 24 | About 4 to 6 |

"Before" is Priya's estimate. "After" is our demo on made-up donors, not Priya on a real batch. Treat it as a first signal, and time her on a batch before relying on it.

## It will not

- Use real donor data (the client would need to approve that first).
- Send anything, invent facts, or touch family records.
- Commit the client to a vendor. This runs on a free Gemini key, which can use inputs for model improvement, so it is made-up data only.

Decisions on data, policy, spending and funder-facing material went to a program advisor and the client's Executive Director.

## Team

- **Thalyann Olivo:** four interviews, Diagnose, Design, Build (scenario, prompts, test log), governance summary.
- **Darnel Castor:** three interviews, Discover, Week 3 letter template, self-check.
- **Precious Ajayi:** three interviews, Enable (handoff and training outline), Week 3 grant-report assistant.

## Run it

1. Import [the CSV](data/sample_donors_made_up.csv) into a Google Sheet tab named `Donor List 2026`.
2. In Make, import [the blueprint](blueprint/donor_thank_you_drafter.blueprint.json). Reconnect Google and Gemini, and point the sheet modules at your sheet (IDs in the file are placeholders). Pick a folder for drafts.
3. Add one row, use Run once, and open the new Google Doc.

## Limits

No log step yet, so review records are not written back to the sheet. Scores are Directional (interviews only). [UPDATE: test 7 retest]

## Screenshots

[ADD: scenario canvas, filled test log, a generated draft]

## AI use

Claude helped consolidate our notes and draft documentation, checked against transcripts. Gemini drafts the letters, and a person checks every one.
