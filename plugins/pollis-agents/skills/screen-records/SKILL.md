---
name: screen-records
description: "Screen the records of a systematic review on your own, as one of two independent screeners: read each record's title and abstract in data/records.csv, decide against the review's inclusion and exclusion criteria whether it goes on to full-text review, and write screening.csv with a decision and a reason for every record, then SCREENING.md, with no questions to anyone. As the third screener, settle the records the two disagreed on. Triggers include 'screen these records', 'title and abstract screening', 'you are one of two screeners', 'settle the screening conflicts', 'Systematic Review'."
metadata:
  updated: "2026-11-15"
---

# Screen Records

You screen the records of a systematic review, started by Pollis from **Compose > Orchestration > Systematic Review...**. Two screeners screen every record, each on its own, as a systematic review's two independent reviewers do: you never see the other screener's decisions. Where the two disagree, the user, or a third screener, settles it.

Nobody will answer questions while you work: decide for yourself.

## What you are given

Your folder, the one you start in, holds:

- **`TASK.md`**: the review's question, its inclusion and exclusion criteria, the outcome and effect it is after, and whether you screen every record or settle conflicts.
- **`SKILL.md`**: this file.
- **`data/records.csv`**: one row for each record, with the columns `id`, `title`, `abstract`, `year`, `authors`, `journal` and `doi`. A record may have no abstract.
- When you settle conflicts, `data/records.csv` has four more columns: `decision_1` and `reason_1`, `decision_2` and `reason_2`, the two screeners' decisions and reasons.

## Rules

- **Stay in your folder.** Write files only there. Read nothing outside it. Do not look for the other screener's folder.
- **Judge each record by its title and abstract alone**, against the criteria of `TASK.md`. Do not look records up on the web.
- **When in doubt, include.** Screening keeps a record that may meet the criteria for the full text to decide. A record with no abstract, whose title does not rule it out, is `unsure`.
- **Never skip a record.** Every `id` of `data/records.csv` gets exactly one row in `screening.csv`.
- **Run no commands other than `julia`**, and `julia` only to read or write the CSV files when there are many records. Read and write files with your own tools.

## What you write

1. **`screening.csv`**, with a header and one row for each record, in the order of `data/records.csv`:

   ```csv
   id,decision,reason
   R0001,include,"Randomised trial of the intervention in adults, outcome reported"
   R0002,exclude,"Population: children"
   R0003,unsure,"No abstract; the title names the intervention"
   ```

   - `decision` is `include`, `exclude` or `unsure`. When you settle conflicts, it is `include` or `exclude` only.
   - `reason` is short: for `exclude`, the criterion the record fails (population, intervention, comparison, outcome, study design, or another of `TASK.md`); for `include` or `unsure`, what it meets or what is missing. Quote the reason when it holds a comma.
2. **`SCREENING.md`**, last, once `screening.csv` is complete: how many records you included, excluded and marked unsure, the criteria that excluded most, and any record that was hard to decide and why.

## Steps

1. Read `TASK.md`, then the header and a few rows of `data/records.csv`.
2. Go through every record in order, and decide.
3. Write `screening.csv`, check that it has one row for each `id`, then write `SCREENING.md`.
4. Stop, with a one-paragraph summary: the counts, and the criterion that excluded most.
