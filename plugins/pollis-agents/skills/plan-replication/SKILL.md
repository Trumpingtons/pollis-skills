---
name: plan-replication
description: "Plan a Pollis replication: read the article of a replication made on its page in Pollis (Compose > Replication > Replicate an Article) and propose, for each target table, how it is computed: by registered Julia packages, by code of the notebook's own, or by a new method, written in the replication's plan for the user to check. Reads and proposes; writes no code. Triggers include 'plan this replication', 'which packages compute these tables', 'Plan with Agent'."
---

# Plan a Replication

Proposes the plan of a Pollis replication: for each target table, how Julia can compute it. You read the article and propose; you write no code and run nothing but small checks of what a package offers. The user checks the plan in the Plan step of the replication's page, which saves each change.

## What you are given

The first message from Pollis names the replication folder. If it does not (the skill was started some other way), ask the user to start this task from Pollis instead, with **Plan with Agent** in the Plan step of the replication's page (**Compose > Replication > Browse Replications**, then the replication's title).

## The three ways

- **`package`**: one or more well-known registered Julia packages compute the table, perhaps with a few lines around them (GLM for a logit, FixedEffectModels for panel regressions, ARCHModels for a GARCH). Most targets are this.
- **`notebook`**: no package does it as the article does, but the computation is short enough to write in the notebook itself (an index, a transformation, a test statistic of a few lines).
- **`method`**: no Julia package has the method, and it is substantial enough that others would want to install and reuse it, with its own tests. Propose this only when neither of the others is reasonable: a replication never needs a method of its own to go on, and the notebook can always hold the code.

## Steps

1. **Read the replication**: `replication.toml`, `INSTRUCTIONS.md` (the user's notes: follow them), every `targets/*.csv` with its `[targets.NAME]` `source`, and any `[plan.NAME]` tables already there. Read the article from its link (`article`, else `https://doi.org/` and the `doi`). **Do not save a copy** of it. If it cannot be read, tell the user and ask for the sections you need.
2. **Find each target in the article**: the model, the estimator and the options that produce it (the sample, the variance estimator, corrections, the random seed of a simulation). Decide whether its values are **exact** or **random**:
   - **exact**: the same on every run, such as estimates, test statistics or a model solved, compared to the digits printed;
   - **random**: they change with the random numbers, so no run gives the digits printed. Examples are a model simulated from its calibration with no data, such as long-run risks, the bootstrap, a random forest, or a neural network such as LSTNet. Find in the article how many runs, samples or seeds each published value averages (`published_runs`, 1 for a single run), and whether it is a mean or another statistic of the runs, such as a median; say so in the note.
3. **Choose the way** for each target, and for `package` the packages. Check that each package does what the plan says, from its documentation, and name the function or option when it is not the obvious one.
4. **Write the plan** in `replication.toml`, one table for each target, replacing one already there only if the user agrees:

   ```toml
   [plan.table3_2]
   way = "package"
   packages = "GLM"
   note = "Logit of column 2 on the regressors of Section 3; robust standard errors (HC1)"

   [plan.table4]
   way = "package"
   packages = "DecisionTree"
   note = "Random forest of 500 trees; test error of one run (p. 12)"
   values = "random"
   runs = 20
   published_runs = 1
   ```

   `way` is `"package"`, `"notebook"` or `"method"`; `packages` the packages, separated by commas, empty for `notebook` and `method`; `note` what computes the table and the options, in one line, with the page or section of the article. For random values, add `values = "random"`, `runs`, how many runs Julia should average (10 when absent; fewer when one run is long, as the training of a neural network), and `published_runs`, how many the article's values average (1 when absent). Exact values need no `values` line.
5. **Report**: the plan, target by target, with which targets are random and the runs of each, and what you could not settle from the article. Then the next steps, on the replication's page: check the plan in **Plan**; **Make a Method**, on the row of a target planned as a new method, if the user wants one; then **Replicate with Agent**.

## Rules

- Change only the `[plan.NAME]` tables of `replication.toml`, and never the `method` line of one: Pollis writes it when it makes a method.
- Never write code or a notebook, and never save copies of the article or the data.
- Never use DataFrames. Never name a university in what you write.
