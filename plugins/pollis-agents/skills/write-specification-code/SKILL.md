---
name: write-specification-code
description: "Write the code of a specification curve: specification.jl, which gives, for every combination of the options kept (each a specification), its rows of the data and its estimate and standard error, then check it on every specification with Pollis' curve.jl until none fails, with no questions to anyone. Triggers include 'write specification.jl', 'write the code of the specification curve', 'Specification Curve'."
metadata:
  updated: "2026-11-14"
---

# Write Specification Code

You write the code of a specification curve, started by Pollis from **Compose > Orchestration > Specification Curve...**. The decisions of the curve and the options the user kept are in `choices.toml`; a **specification** is one option of each decision. You write **`specification.jl`**, which gives each specification's rows of the data and its estimate. Pollis' `curve.jl`, already in your folder, runs it on every specification for the curve, and on hundreds of resamples of each specification's rows for the test under the null.

Nobody will answer questions while you work: decide for yourself, and say in a comment of `specification.jl` how you read an option whose meaning was not plain.

## What you are given

Your folder, the one you start in, holds:

- **`TASK.md`**: the question, the estimate every specification must give and its unit, the data, the decisions and options kept, and the Julia command that checks your code.
- **`SKILL.md`**: this file.
- **`choices.toml`**: the decisions and options kept, each with its `id` and `label`.
- **`curve.jl`**: written by Pollis. Do not change it.
- **`data/`**, when the data are a file: a copy of the file and `README.md`, which says what its columns are. When the data are datasets of PollisDatasets, `TASK.md` says how to load them.

## Rules

- **Stay in your folder.** Write files only there, and only `specification.jl` and files of your own for trying things out. Do not change `curve.jl` or `choices.toml`.
- **Implement every option kept, as its label says.** Do not drop, merge or add options, and do not make an option behave like another to make it run.
- **Every specification gives the estimate of `TASK.md`**, in its unit.
- **Run commands only with `julia`**, as `TASK.md` shows; read and write files with your own tools.
- **Never make up a number.** If an option cannot be implemented, make its specifications throw an error that says why, and say so in your summary.

## What you write

**`specification.jl`** defines two functions. `curve.jl` includes it, after `using Pollis`, and calls them:

```julia
Pollis.packages("GLM", "StatsBase")
using GLM, StatsBase, PollisDatasets

# Read the data once, when the file is included
const DATA = ...

"""
The rows of a specification: a NamedTuple of columns of equal length, with `y` the outcome and `d` the variable
whose effect is estimated, then every column `estimate` needs.
"""
function specification_data(spec::Dict{String,String})
    # spec["sample"] == "drop_zero_earnings", spec["estimator"] == "ols", ...
    return (y = ..., d = ..., age = ..., education = ...)
end

"""
The estimate and its standard error on `rows`: the columns `specification_data` returned, or a resample of them.
"""
function estimate(rows, spec::Dict{String,String})
    ...
    return (b, se)
end
```

- **`spec`** maps each decision's `id` to the `id` of its option, as `choices.toml` has them.
- **`specification_data`** applies the options that choose rows and columns: comparison group, exclusions, outliers, which controls. It returns plain vectors, `y` and `d` among them, all as long as `y`, with no missing values. `d` is 0 or 1 for a treatment; any other numbers are allowed for a regressor.
- **`estimate`** applies the options that choose the method. It must use only `rows`, never `DATA`, because the test under the null calls it on resamples of the rows, with `y` changed: anything it computes, such as a propensity score, it computes again from `rows`. It returns the estimate and its standard error as finite numbers, the standard error positive.
- Both functions are **called from several threads at once**: change no global variable, and set no global seed. Where an estimator draws at random, make a local generator, such as `Random.Xoshiro(1)`, and pass it on.
- **Speed matters**: the test runs `estimate` hundreds of times for every specification. Read the data once, at the top of the file; prefer closed-form estimators and their standard errors to a bootstrap inside `estimate`.
- Use Tables.jl tables, such as the column tables `dataset` returns or `CSV.File`, never DataFrames.

## Steps

1. Read `TASK.md` and `choices.toml`, then what the data's columns are. Look at the data with Julia in a file of your own.
2. Write `specification.jl`, with a comment for each option saying how it is implemented.
3. Run the check of `TASK.md`, `julia -L <Pollis.jl> curve.jl check`, from your folder. It runs every specification once, writes `check.json` and prints each specification that fails. Fix `specification.jl` until every specification runs.
4. Look at a few estimates: they are in the unit asked for, of a plausible size, and options that should matter do.
5. Stop, with a one-paragraph summary: how you implemented the options, any option you read in a way its label leaves open, and the range of the estimates you saw.
