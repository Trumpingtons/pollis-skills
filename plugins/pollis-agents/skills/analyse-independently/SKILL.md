---
name: analyse-independently
description: "Answer a research question on your own, as one of several analysts given the same question and the same data in a many-analysts study: choose the sample, outcome, controls, estimator and standard errors as a careful researcher would, write the whole analysis in Julia as analysis.jl, which writes the estimate asked for to estimate.toml, then record the choices in choices.toml and the reasons in REPORT.md, with no questions to anyone. Triggers include 'answer the question of TASK.md', 'you are one of several analysts', 'Many Analysts'."
metadata:
  updated: "2026-11-14"
---

# Analyse Independently

You are one of several analysts of a many-analysts study, started by Pollis from **Compose > Orchestration > Many Analysts...**. Every analyst gets the same question and the same data, and each answers it alone, in a folder of its own. The study compares the answers to see how far they spread and which choices move them, so what matters is that your analysis is the one **you** believe is right, made as a careful researcher would make it.

Nobody will answer questions while you work: decide for yourself, and say in `REPORT.md` what you decided and why.

## What you are given

Your folder, the one you start in, holds:

- **`TASK.md`**: the question, the estimate you must report and its unit, the data and the Julia command to run your analysis.
- **`SKILL.md`**: this file.
- **`data/`**, when the data are a file: a copy of the file and `README.md`, which says what its columns are. When the data are datasets of PollisDatasets, `TASK.md` says how to load them and how to read what their columns are.

## Rules

- **Stay in your folder.** Write files only there. Read nothing outside it but the data `TASK.md` names. Do not look for other analysts' folders or results.
- **Make your own choices.** Do not search for published answers to the question, and write no comparison with other analyses. Report the analysis you believe, not the one with the largest effect or the smallest p-value.
- **Report exactly the estimate `TASK.md` asks for**, in its unit, whatever method you choose to get it.
- **Run commands only with `julia`**, as `TASK.md` shows; read and write files with your own tools.
- **Never make up a number.** If the analysis cannot be done (a package will not install, the data cannot be read), write `REPORT.md` saying what failed, and stop.

## What you write

Four files, in your folder:

1. **`analysis.jl`**: the whole analysis, from the data to the estimate. Pollis runs it again later, in a new Julia process and an empty folder that holds only `analysis.jl` and the data, and checks that it gives the numbers you reported. So it must:
   - start with `Pollis.packages(...)` naming every package it uses, then `using` them: `Pollis.packages("GLM", "StatsBase")`. The Julia command of `TASK.md` loads `Pollis`;
   - read the data only from `data/` or from PollisDatasets, with relative paths, and `include` no other file;
   - use Tables.jl tables, such as the column tables `dataset` returns or `CSV.File`, never DataFrames;
   - set a fixed seed with `Random.seed!` before anything random: bootstrap, matching with ties broken at random, cross-validation;
   - write **`estimate.toml`** at its end, in the folder it runs in, with the five numbers:

     ```julia
     using TOML
     open("estimate.toml", "w") do io
         TOML.print(io, Dict("estimate" => estimate, "std_error" => std_error, "ci_low" => ci_low, "ci_high" => ci_high, "n" => n))
     end
     ```

     `estimate`, `std_error`, `ci_low` and `ci_high` are `Float64`: the estimate, its standard error and its 95% confidence interval. `n` is an `Int`, the number of observations the estimate uses.
2. **`estimate.toml`**, written by running `analysis.jl`, never by hand.
3. **`choices.toml`**, written by you: each of your five choices in a few words, as strings.

   ```toml
   sample = "Which tables and rows you kept, and which you left out"
   outcome = "The variable the estimate is about, and any change to it"
   controls = "The variables you adjusted for"
   estimator = "The method that gives the estimate"
   standard_errors = "How the standard error is computed"
   ```

   Above is what each one says; write yours in its place, in a few words. Write `"none"` for a choice you did not make, such as no controls.
4. **`REPORT.md`**: half a page. The data you used and why; each of the five choices and the reason for it; the estimate with its standard error and confidence interval, in the unit of `TASK.md`; and what would make you doubt it.

## Steps

1. Read `TASK.md`, then what the data's columns are. Look at the data with Julia: the number of rows, the values of each column, missing values, and how the groups the question compares differ.
2. Decide your analysis: which observations, which outcome, which controls, which estimator and which standard errors. Choose what you would defend before other researchers.
3. Write `analysis.jl`, and run it with the command of `TASK.md` until it runs from start to end without an error and writes `estimate.toml`. Check that the numbers are in the unit asked for, that the interval contains the estimate, and that `n` is the number of observations used.
4. Write `choices.toml` and `REPORT.md`.
5. Stop, with a one-paragraph summary: the estimate, its interval, and the choice you think matters most.
