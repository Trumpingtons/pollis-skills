---
name: replicate-article
description: "Replicate an article in a Pollis replication: write the replication's Julia notebook, in a folder made on its page in Pollis (Compose > Article Replication > Replicate an Article), so that it reproduces the article's published tables (the targets) from its data, each printed value beside Julia's in results.csv, through Pollis.replicate and Pollis.compare. Triggers include 'replicate this article', 'write the replication notebook', 'reproduce the published tables', 'Replicate with Agent'."
metadata:
  updated: "2026-11-10"
---

# Replicate an Article

Writes the notebook of a Pollis replication: the analysis of a published article, in Julia, run on the article's data, with every value of its published tables set beside Julia's. The user has made the replication's folder on its page in Pollis (**Compose > Article Replication > Replicate an Article...**): the links to the article and the data, and the published tables, typed in as printed. You write `NAME.ipynb` so that each target table is computed from the data and compared with the printed values, and run it until as many as can be are reproduced.

## What you are given

The first message from Pollis names the replication folder and the file of Pollis' Julia functions, `Pollis.jl`. If it does not (the skill was started some other way), ask the user to start this task from Pollis instead, with **Replicate with Agent** on the replication's page (**Compose > Article Replication > Browse Replications**, then the replication's title). Do not look for Pollis' files yourself.

It needs Julia (`julia --version`) and the internet, to read the article and download the data. If Julia is missing, tell the user to install it with juliaup and stop.

## The replication folder

- `replication.toml`: `name` (NAME, also the notebook's name), `title`, `citation`, `doi`, `article` (the link to read the article), `[inputs.NAME]` (the input tables: `url`, a link, or `file`, a table typed in from the article in `inputs/`; `source`; `licence`; `delim`, `columns`, `skipto`, `limit` to read it), `[targets.NAME]` (`source`: where each target is published).
- `targets/NAME.csv`: a published table, the row labels in the first column and every value as printed. These are what the replication is compared with: never change them, except a value the user confirms was mistyped.
- `[plan.NAME]` in `replication.toml`, when there is one: how each target is computed. `way = "package"` with the Julia packages in `packages`; `way = "notebook"`, code of the notebook's own; `way = "method"`, the method in `~/.pollis/methods/` named by `method`, whose `src/NAME.jl` the notebook includes, or, when the method is not written yet, code of the notebook's own. `note` says what computes the table. Follow the plan; where it proves wrong, say so and why.
- `INSTRUCTIONS.md`: the user's notes. Follow them: they win over your own reading of the article.
- `NAME.ipynb`: the notebook you write. `results.csv`: written by `Pollis.replicate` and `Pollis.compare` when the notebook runs in the folder. `PROGRESS.md`: your record (below).

## Pollis.replicate and Pollis.compare

Pollis loads its functions in every Julia session it starts, the notebook's among them. In a session of your own, `include` the `Pollis.jl` Pollis named, and run it in the replication folder, so `results.csv` is written there:

```julia
cd("<the replication folder>"); include("<the Pollis.jl named>")
```

- `Pollis.replicate(title, rows)` for values exact to the digits printed (estimates, test statistics, a model solved): each row is `(label, value, published)`, `published` the string as printed, whose decimals give the precision.
- `Pollis.compare(title, rows; runs, published_runs)` for random values, those that change with the random numbers, so that no run gives the digits printed: a model simulated from its calibration, the bootstrap, a random forest, a neural network such as LSTNet. Run the computation once for each of `runs` seeds (the seed of each run its number, `Random.seed!(seed)` or an `rng` argument), and give each row as `(label, mean(values), std(values) / sqrt(runs), published)`, the mean of the runs and its Monte Carlo standard error. `published_runs` is how many runs the article's value averages, 1 for a single run: the published value is noisy too, and `Pollis.compare` measures the difference against the noise of both. Without `published_runs`, `noisy = true` takes the published value to average as many runs as Julia's, and `noisy = false` takes it as exact.
- Which targets are random, and their `runs` and `published_runs`, are in the `[plan.NAME]` tables of `replication.toml` (`values = "random"`; `runs` 10 and `published_runs` 1 when absent). A target the plan does not mark may be random too: if the article's values come from random numbers, say so to the user, and compare them with `Pollis.compare`. Never compare random values with `Pollis.replicate`, and never pick seeds until a run matches the digits printed.
- When the article reports another statistic of its runs than the mean, such as the median of simulated samples, compute that statistic of Julia's runs, and its standard error by the bootstrap over the runs; say so in the notebook.
- `Pollis.packages("CSV", "GLM", ...)`: the notebook's first code cell, which installs what is missing.

Give each call the target's name as `title` (for example `"table3_2"`), and each row a label made of the target's row label and column (for example `"income, (2)"`), so that Pollis can show the results table by table. Take every `published` string from `targets/NAME.csv`, read as strings (`CSV.File(path; types = String)`), never typed in again: the targets are what the user checked against the article.

## Save your work as you go

A replication can take hours and a session can stop at any point, so keep `PROGRESS.md` in the folder, the record a new session starts from: for each target, its table, the model, the estimator and the options found, and each open point with how it was settled; where each target stands (not started, written, reproduced, or off: which values, by how many units of the last digit); what was tried for values that stay off; the next step. Write it once you have read the article, and update it after each run and before you stop.

## Steps

0. **If `PROGRESS.md` exists**, read it first and go on from its next step.
1. **Read the replication**: `replication.toml`, `INSTRUCTIONS.md`, every `targets/*.csv`. Read the article from its link (`article`, else `https://doi.org/` and the `doi`), and the data from its links. **Do not save copies** of the article or the data, not even in a temporary folder: read them into memory (for example `CSV.File(Downloads.download(url, IOBuffer()))`). If the article cannot be read (behind a paywall, say), tell the user and ask for the sections you need; never guess the analysis from its title.
2. **Find each target in the article**: the table it comes from (its `source`), what each row and column is, the model, the estimator, the options (degrees-of-freedom corrections, the variance estimator, logs or levels, the sample used, the random seed of a simulation). Say in a few lines what you found before you write code, and ask about anything you cannot settle from the article and INSTRUCTIONS.md.
3. **Write `NAME.ipynb`**, a Jupyter notebook with the Julia kernel, written with no outputs (`"execution_count": null`, `"outputs": []`), in the shape of the replications that come with Pollis:
   - a Markdown cell with the title (`# ` heading) and a paragraph on the article and what it found, then one on the data and the packages;
   - the `Pollis.packages(...)` cell, then a **Set-up** section that reads the inputs, from their links into memory or from `inputs/`, as Tables.jl column tables;
   - one section for each target, `## 1. Table 3.2: ...`, a Markdown cell saying what the table holds and how it is computed, with the pages of the article, then the code that computes it and its `Pollis.replicate` or `Pollis.compare` call;
   - a last section, **What the Comparisons Show**, written once the notebook has run: which values are reproduced and which are not, with both values side by side.
   
   In its metadata, `"pollis": {"workflow": {"updated": "YYYY-MM-DD", "article": {"citation": ..., "doi": ...}, "datasets": [{"name": ..., "source": ...}]}}`. Use well-known registered packages, Tables.jl for tables, never DataFrames. Check that the file is valid JSON.
4. **Run it**: check each section's code as you write it, in one Julia session started as above, then run all the cells in order, so that `results.csv` holds every target. Fix the code, not the targets. If a value in a target looks mistyped (two digits swapped compared with the article), show the user both and let them correct it.
5. **When values stay off**, look for the usual causes first: the sample (missing values, the years used), the degrees of freedom, the variance estimator, the starting values or the optimiser of an iterative estimate, the random seed. For a random target, check first `published_runs` against the article, and the statistic of the runs it reports. If a value cannot be reproduced after these, say which, by how much, and what you checked: the user then gives each a kind and an explanation in **Compare**, or has **Explain with Agent** propose them.
6. **Report**: for each target, the values reproduced and those not, with the values side by side (Julia's, rounded to the digits printed, and the article's), never just "identical" or "matches"; what you assumed where the article was unclear. Then the next step: **Record and Share**, on the replication's page, which reads `results.csv`.

## Rules

- Edit only the replication folder: `NAME.ipynb`, `PROGRESS.md`, and `targets/` only to fix a value the user confirmed was mistyped. Never add `article_local` or `local` to `replication.toml`, and never change its `licence`, `done`, the precision of its `[targets.NAME]` tables (`decimals`, `standard_errors`), `[plan.NAME]` or `[[differences]]`.
- Never type in a result: every value compared is computed by the notebook from the data.
- Never use DataFrames. Never name a university in what you write.
