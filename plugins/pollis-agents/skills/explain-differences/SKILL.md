---
name: explain-differences
description: "Explain the differences of a Pollis replication: for each published value that Julia does not reproduce in results.csv, look into why and propose its kind (misprint, rounding, not replicable, unexplained) and an explanation, written in the replication's record for the user to check. Changes no code. Triggers include 'explain the differences', 'why does Julia not reproduce these values', 'Explain with Agent'."
metadata:
  updated: "2026-11-10"
---

# Explain the Differences

Looks into each published value of a Pollis replication that Julia does not reproduce, and proposes what the difference comes from. You investigate and propose; you change neither the notebook nor the targets. The user checks the explanations in the Compare step of the replication's page.

## What you are given

The first message from Pollis names the replication folder and the file of Pollis' Julia functions, `Pollis.jl`. If it does not (the skill was started some other way), ask the user to start this task from Pollis instead, with **Explain with Agent** in the Compare step of the replication's page (**Compose > Replication > Browse Replications**, then the replication's title).

## The kinds

- **`misprint`**: the article printed a wrong value: two digits swapped, a sign lost, a value that contradicts others of the same table (a t statistic that is not the coefficient over its standard error, a total that is not the sum). Say what shows it.
- **`rounding`**: the value is off by one in the last digit because the article rounded an intermediate result, or rounded half differently, or truncated.
- **`not replicable`**: the value cannot be had from the published data: the article used data not published, a sample it does not describe, a random seed or a software default it does not give. Say what is missing.
- **`unexplained`**: none of these can be shown. This is an answer too: say what you checked.

## Steps

1. **Read the replication**: `replication.toml` (with its `[plan.NAME]` tables and any `[[differences]]` already there), `INSTRUCTIONS.md`, `PROGRESS.md` if it exists, the notebook `NAME.ipynb`, `results.csv` and the `targets/*.csv`. The values not reproduced are the rows of `results.csv` whose verdict is not "same to" all the printed decimals, nor "within noise". Read the article from its link; **do not save copies** of it or of the data.
2. **Look into each value**, the usual causes first. For a random value, compared with `Pollis.compare`, check that `published_runs` in its `[plan.NAME]` table is what the article says (one run is noisier than a mean of many), and that Julia computes the same statistic of its runs as the article (a mean, a median); a random value compared with `Pollis.replicate` is not a difference to explain but a notebook to fix (step 3). Then: the sample (missing values, the years used), the degrees of freedom, the variance estimator, the starting values or the optimiser of an iterative estimate, the random seed, and the other values of the same row and column, which can show a misprint. To check a cause, run code in a Julia session of your own, started in the replication folder with `include` of the `Pollis.jl` Pollis named, with `results = nothing` in any `Pollis.replicate` or `Pollis.compare` call, so `results.csv` stays as the notebook wrote it.
3. **If the notebook is wrong** (the sample or an option differs from the article's), do not change it: say so in the report, with the fix and the values it gives, so the user can run **Replicate with Agent** again or change the notebook.
4. **Write the explanations** in `replication.toml`, one `[[differences]]` table for each value, its `target` and `row` exactly as in `results.csv` (its `table` and `row` columns), replacing one already there for the same value only if the user agrees:

   ```toml
   [[differences]]
   target = "table3_2"
   row = "income, (2)"
   kind = "misprint"
   explanation = "Printed 0.135; the t statistic of the same row, 4.21, gives 0.153 with its standard error 0.0363"
   ```

5. **Report**: each value with the article's and Julia's side by side, never "identical" or "matches", its kind and its explanation; and any fix of the notebook found in step 3.

## Rules

- Change only the `[[differences]]` tables of `replication.toml`. Never edit the notebook, the targets, `results.csv` or the rest of the record.
- Never save copies of the article or the data. Never use DataFrames. Never name a university in what you write.
