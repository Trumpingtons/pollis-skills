---
name: write-method
description: "Write a Pollis method: the Julia code of a published statistical method, in a method folder made on its page in Pollis (Compose > Article Replication > Create Article Method), so that it reproduces the article's tables (the targets) to the digits printed, tested with Pollis' package script. Triggers include 'write a method', 'write the code of this method', 'reproduce the tables of this article', 'implement the method in this folder', 'make the targets pass', 'Write a Method'. The prepare-method skill then brings it up to the Pollis registry rules."
metadata:
  updated: "2026-11-10"
---

# Write a Method

Writes the Julia code of a Pollis method: a published statistical method whose tests are the article's own tables. The user has made the method's folder on its page in Pollis (**Compose > Article Replication > Create Article Method**): the links to the article and the data, and the published tables, typed in as printed. You write `src/NAME.jl` so that each table is computed from the data, and test it until every printed value is reproduced. Then you add reference tests, which keep the method's results in full from changing unnoticed, and property tests, which check rules its results must obey.

Read `reference.md`, next to this SKILL.md, before anything else: the method folder, method.toml, the target tables, the test command, the reference and property tests, and the registry rules.

## What you are given

The first message from Pollis names the method folder and gives the test command, with its paths. If it does not (the skill was started some other way):

- **The folder**: the current folder if it has a `method.toml`; else list the folders of `~/.pollis/methods` that have one, and ask which.
- **The test command**: ask the user to start this task from Pollis instead, with **Write Method** on the method's page (**Compose > Article Replication > Browse Article Methods**, then the method's title), which gives it. Do not look for Pollis' script yourself.

It needs Julia (`julia --version`) and the internet, to read the article and download the data. If Julia is missing, tell the user to install it with juliaup and stop.

## Save your work as you go

Writing a method can take hours and a session can stop at any point, so keep `PROGRESS.md` in the method folder, the record a new session starts from:

- **What the article settles**: for each target, its table, the model, the estimator and the options found in step 2, and each open point with how it was settled (by the article, INSTRUCTIONS.md or the user).
- **Where each target stands**: not started, written, passing, or off (which values, by how many units of the last digit).
- **The reference and property tests**: the results saved and their tolerance, the properties and why each must hold, and whether the references are saved in the method folder.
- **What was tried** for values that stay off, and what it changed.
- **The next step.**

Write it at the end of step 2, and update it after each test run and before you stop. Save the code in `src/NAME.jl` and the calls in method.toml as soon as each target's function works, not at the end.

To save time and tokens, check a target you are working on by calling its function in one Julia session (`include` the code, then the call), not with the test command, which reruns every target: run the test command when a target looks done, and leave the slow targets (long grids, bootstraps, simulations) for last.

## Steps

0. **If `PROGRESS.md` exists**, read it first and go on from its next step: do not redo the reading of steps 1 and 2 for the targets it already covers, and read the article again only for what it leaves open.
1. **Read the method**: `method.toml`, `INSTRUCTIONS.md` (the user's notes: follow them, they win over your own reading of the article), every `targets/*.csv` and its `[targets.NAME]` table. Read the article from its link (`article`, else `https://doi.org/` and the `doi`), and the input data from its links. **Do not save copies** of the article or the data, not even in a temporary folder: read them into memory (for example `Downloads.download(url, IOBuffer())` in Julia) or read the web page. If the article cannot be read (behind a paywall, say), tell the user and ask for the sections you need; never guess the method from its title.
2. **Find each target in the article**: the table or the passage it comes from (its `source`), what each row and column is, the model, the estimator, the options (degrees-of-freedom corrections, which variance estimator, logs or levels, the sample used). Note anything the article leaves open. Say in a few lines what you found before you write code, and ask about anything you cannot settle from the article and INSTRUCTIONS.md.
3. **Write `src/NAME.jl`** (module NAME): one exported function for each thing the method computes, taking the inputs as Tables.jl column tables and returning a matrix laid out like the target (its rows and columns in the target's order, without the label column). Replace the stub Pollis wrote. Use well-known registered packages and list them in `packages` in method.toml; Tables.jl for tables, never DataFrames; Enzyme rather than Zygote when the method needs reverse-mode derivatives. Give every exported function a docstring that says what it computes, what it takes and what it returns, and which table of the article it reproduces.
4. **Write the calls**: for each target, `call` in its `[targets.NAME]` table, the Julia expression that computes it from the inputs (each input a variable named after its `[inputs.NAME]` table). Write the calls that are missing; change one the user wrote only if it cannot work, and say why. Leave `tolerance` at 0 unless the user agrees to raise it (step 6).
5. **Run the test command** and read the output. A failure names the target (its `source`), the row and the column. Fix the code, not the targets: a target is what the article printed. If a value in a target looks mistyped (a digit swapped compared with the article), show the user both and let them correct it.
6. **When values stay off**, compare yours with the printed ones, value by value, rounded to the digits printed: say how far each is, in units of the last digit. Look for the usual causes first: the sample (missing values, the years used), the degrees of freedom, the variance estimator, the starting values or the optimiser of an iterative estimate, the random seed of a simulation. If the article cannot be reproduced exactly (a value off by one in the last digit after all of these, say), tell the user which values, by how much, and what you checked, and let them decide whether to raise that target's `tolerance`.
7. **Add reference and property tests**, once every target passes or the user has settled those that stay off: `tests/tests.jl`, `tests/references/` and the `[tests]` table, as `reference.md` describes under *Reference and property tests*. Save the result of each target's call in full and the method's other results the targets do not check; give a random result a reference test only through an `rng` keyword and a `StableRNG`; write one to three properties that follow from the method, each with the reason in a comment. Save the references as `reference.md` says: run the test command, check what it saved, copy the files into `tests/references/`, and run it again, until it passes with no reference saved. A property that fails is a finding to report, not a test to loosen. If `src/` steps a differential equation with a scheme of its own, add a convergence test of its order, as `reference.md` describes under *Convergence tests*.
8. **Add a usage example** to method.toml: `usage = """..."""`, Julia code that loads the data and calls the method, as a user would. It goes in the package's README.
9. **Report**: for each target, the values reproduced and those not, with the values side by side (yours, rounded to the digits printed, and the article's), never just "identical" or "matches"; the tolerance of each target; the reference tests (what each saves and its tolerance), the property tests (each rule and why it holds) and the convergence test, if any (the order expected, where it comes from, and the order estimated); what you assumed where the article was unclear. Then the next steps, on the method's page: **Prepare for Registration** if the test output lists registry rules still broken, and **Package and Publish** to publish.

## Rules

- Edit only the method folder: `method.toml`, `src/`, `tests/`, `PROGRESS.md`, and `targets/` only to fix a value the user confirmed was mistyped. Never edit the package the test command makes (it is rewritten each run), and never add `article_local` or `local`.
- Never type in a result: every value the tests check is computed by the code from the data, and every reference is saved by ReferenceTests from a run, never written by hand.
- Never use DataFrames. Never name a university in what you write.
