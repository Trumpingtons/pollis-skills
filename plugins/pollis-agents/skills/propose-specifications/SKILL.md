---
name: propose-specifications
description: "Propose the specifications of a specification curve, on your own, as one of several agents given the same question and the same data: look at the data in Julia, list the decisions an analyst faces in answering the question and the defensible options of each, with the reason for each option, in decisions.toml, with no questions to anyone. Triggers include 'propose the decisions of TASK.md', 'you are one of several agents who list the decisions', 'Specification Curve'."
metadata:
  updated: "2026-11-14"
---

# Propose Specifications

You are one of several agents of a specification curve, started by Pollis from **Compose > Orchestration > Specification Curve...**. Every agent gets the same question and the same data, and each lists, alone, the **decisions** an analyst faces in answering the question and the **options** of each that a careful researcher would defend. The user then merges the lists, keeps the options they defend, and Pollis runs every combination of one option of each decision: each combination is a specification, and their estimates in order are the curve.

Nobody will answer questions while you work: decide for yourself.

## What you are given

Your folder, the one you start in, holds:

- **`TASK.md`**: the question, the estimate every specification must give and its unit, the data and the Julia command to look at them.
- **`SKILL.md`**: this file.
- **`data/`**, when the data are a file: a copy of the file and `README.md`, which says what its columns are. When the data are datasets of PollisDatasets, `TASK.md` says how to load them and how to read what their columns are.

## Rules

- **Stay in your folder.** Write files only there. Read nothing outside it but the data `TASK.md` names. Do not look for other agents' folders.
- **Propose what a careful researcher would defend**, theoretically justified, statistically valid and not redundant: not every possible option, and not only the one you would choose. Do not search for published answers to the question.
- **Keep the estimate of `TASK.md` fixed.** Every option must lead to the same quantity in the same unit. A different outcome measure (a logarithm in place of a level, a share in place of a count) changes the estimate, so it is not an option. The standard error is not a decision either: every specification reports its own.
- **Options of a decision must exclude each other**: a specification takes exactly one option of each decision. Options that would give the same rows and the same estimate are one option.
- **Keep the curve small enough to run**: from 2 to 6 decisions, from 2 to 5 options each, and at most a few hundred combinations in all. Leave out a decision that you think barely moves the estimate.
- **Run commands only with `julia`**, as `TASK.md` shows; read and write files with your own tools.

## What you write

**`decisions.toml`**, in your folder:

```toml
[[decisions]]
id = "outliers"
label = "Outliers"

[[decisions.options]]
id = "keep_all"
label = "Keep every row"
reason = "No rule for outliers was set before looking at the data"

[[decisions.options]]
id = "trim_top_1pct"
label = "Drop the top 1% of the outcome"
reason = "A few extreme values can dominate a mean difference"

[[decisions]]
id = "estimator"
label = "Estimator"

[[decisions.options]]
id = "ols_controls"
label = "OLS with controls"
reason = "Adjusts for observed differences linearly"
```

Above is the form, with content that has nothing to do with your task; write yours in its place.

- **`id`**: lower-case letters, digits and underscores, starting with a letter, at most 60 characters. Ids are how the lists of the agents are merged, so use the plain words any analyst would: `sample`, `controls`, `estimator`, `comparison_group`, `outliers`; and for options, what they are: `all_rows`, `drop_incomplete_rows`, `ols`, `propensity_matching`.
- **`label`**: what the user reads, in a few words, title-free.
- **`reason`**: one sentence, why a careful researcher would defend the option.

Write it with your own tools, and check it parses: `julia -e 'using TOML; TOML.parsefile("decisions.toml")'`, with the Julia command of `TASK.md`.

## Steps

1. Read `TASK.md`, then what the data's columns are. Look at the data with Julia, in a file `explore.jl` of your folder: the number of rows, the values of each column, missing values, outliers, and how the groups the question compares differ.
2. List the points of an analysis of the question where careful researchers would choose differently: which rows (comparison groups, exclusions, outliers), which controls, which estimator, and any other you find. For each, the options you would defend.
3. Write `decisions.toml`, and check it parses.
4. Stop, with a one-paragraph summary: the decisions, the number of combinations, and the decision you think moves the estimate most.
