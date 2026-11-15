---
name: extract-effect-sizes
description: "Extract the data of the studies of a systematic review on your own, as one of two independent extractors: read each study's full text, or its abstract when there is no full text, check that it meets the review's criteria, and write extraction.csv with the effect on the review's measure, its standard error, the sample size and where in the study each value is, then EXTRACTION.md, with no questions to anyone. Triggers include 'extract the effect sizes', 'data extraction for the meta-analysis', 'you are one of two extractors', 'Systematic Review'."
metadata:
  updated: "2026-11-15"
---

# Extract Effect Sizes

You extract the data of the studies of a systematic review, started by Pollis from **Compose > Orchestration > Systematic Review...**, once its records have been screened. Two extractors extract every study, each on its own, as a systematic review's two independent reviewers do: you never see the other extractor's values. Pollis then shows the user where the two differ, the user settles it, and Pollis runs the meta-analysis on the values kept.

Nobody will answer questions while you work: decide for yourself, and say in the notes what you could not find.

## What you are given

Your folder, the one you start in, holds:

- **`TASK.md`**: the review's question, its inclusion and exclusion criteria, the outcome and the **effect measure** to extract, and the Julia command.
- **`SKILL.md`**: this file.
- **`data/studies.csv`**: one row for each study, with the columns `id`, `title`, `year`, `authors`, `journal`, `doi`, `abstract` and `full_text`, the file of its full text in `data/full-texts/` or empty when there is none.
- **`data/full-texts/`**: each full text as a PDF, `<id>.pdf`, and its text, page by page, each page after a form feed, as `<id>.txt`.

## Rules

- **Stay in your folder.** Write files only there. Read nothing outside it. Do not look for the other extractor's folder, nor the studies on the web.
- **Every value comes from the study.** Note where: a table, a figure, a page, as the study numbers them. Never make up a value, and never take one from another study.
- **The effect is on the measure of `TASK.md`**, for the outcome of `TASK.md`, with the sign the measure has there (for a difference, the intervention minus the comparison). When a study reports it with its standard error, take them. When it reports a 95% confidence interval instead, the standard error is the interval's width divided by 2 × 1.96, on the scale of the measure (the log scale for a ratio). When it reports only the statistics the measure is computed from (means, standard deviations and sizes of each group; the counts of a 2 × 2 table; a correlation and a sample size), compute the measure and its standard error from them with the usual formulas of the Cochrane Handbook, in Julia, and say in `how` what you computed from what.
- **One effect for each study.** When a study reports several (several times, measures or arms), take the one closest to the outcome of `TASK.md`, and say in `notes` which and why.
- **Eligibility comes first.** A study that, read in full, does not meet the criteria is `eligible = no`, with the criterion in `reason`, and no effect.
- **A study with no full text** is judged on its abstract. Extract what the abstract gives, and say so in `notes`.
- **Run commands only with `julia`**, with the command of `TASK.md`, to compute an effect or read the text files. Read and write files with your own tools.

## What you write

1. **`extraction.csv`**, with a header and one row for each study of `data/studies.csv`, in its order:

   ```csv
   id,eligible,reason,effect,se,n,how,where,notes
   R0004,yes,,0.42,0.15,128,reported,"Table 3, p. 7",
   R0011,yes,,-0.31,0.12,96,"computed from the means, SDs and sizes of each group","Table 2",""
   R0017,no,"Study design: not randomised",,,,,,
   R0023,yes,,,,,,,"The outcome is reported only as a figure, with no values"
   ```

   - `eligible` is `yes` or `no`; `reason` says why for `no`.
   - `effect` and `se` are numbers with a point for decimals, on the measure of `TASK.md`; empty when the study does not give enough to compute them, with why in `notes`.
   - `n` is the number of participants the effect is computed on.
   - `how` is `reported`, or what you computed the effect from.
   - Quote any value that holds a comma.
2. **`EXTRACTION.md`**, last, once `extraction.csv` is complete: for each study, a line with its effect and where it comes from, and the studies you found hard, and why.

## Steps

1. Read `TASK.md` and `data/studies.csv`.
2. For each study: read its full text (the `.txt`, or the PDF for a table the text does not keep), or its abstract; decide whether it is eligible; find or compute the effect, its standard error and the sample size.
3. Write `extraction.csv`, check that it has one row for each `id`, then write `EXTRACTION.md`.
4. Stop, with a one-paragraph summary: how many studies are eligible, how many have an effect, and the hardest to extract.
