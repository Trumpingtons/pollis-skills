---
name: write-method
description: "Write a Pollis method: the Julia code of a published statistical method, in a method folder made with Compose > New Methods > Create Method, so that it reproduces the article's tables (the targets) to the digits printed, tested with Pollis' package script. Triggers include 'write a method', 'write the code of this method', 'reproduce the tables of this article', 'implement the method in this folder', 'make the targets pass', 'Write a Method'. The prepare-method skill then brings it up to the Pollis registry rules."
---

# Write a Method

Writes the Julia code of a Pollis method: a published statistical method whose tests are the article's own tables. The user has made the method's folder in Pollis (**Compose > New Methods > Create Method...**): its name, the links to the article and the data, and the published tables, typed in as printed. You write `src/NAME.jl` so that each table is computed from the data, and test it until every printed value is reproduced.

Read `reference.md`, next to this SKILL.md, before anything else: the method folder, method.toml, the target tables, the test command and the registry rules.

## What you are given

The first message from Pollis names the method folder and gives the test command, with its paths. If it does not (the skill was started some other way):

- **The folder**: the current folder if it has a `method.toml`; else list the folders of `~/.pollis/methods` that have one, and ask which.
- **The test command**: ask the user to start this task from Pollis instead, with **Compose > New Methods > Write a Method...**, which gives it. Do not look for Pollis' script yourself.

It needs Julia (`julia --version`) and the internet, to read the article and download the data. If Julia is missing, tell the user to install it with juliaup and stop.

## Steps

1. **Read the method**: `method.toml`, `INSTRUCTIONS.md` (the user's notes: follow them, they win over your own reading of the article), every `targets/*.csv` and its `[targets.NAME]` table. Read the article from its link (`article`, else `https://doi.org/` and the `doi`), and the input data from its links. **Do not save copies** of the article or the data, not even in a temporary folder: read them into memory (for example `Downloads.download(url, IOBuffer())` in Julia) or read the web page. If the article cannot be read (behind a paywall, say), tell the user and ask for the sections you need; never guess the method from its title.
2. **Find each target in the article**: the table or the passage it comes from (its `source`), what each row and column is, the model, the estimator, the options (degrees-of-freedom corrections, which variance estimator, logs or levels, the sample used). Note anything the article leaves open. Say in a few lines what you found before you write code, and ask about anything you cannot settle from the article and INSTRUCTIONS.md.
3. **Write `src/NAME.jl`** (module NAME): one exported function for each thing the method computes, taking the inputs as Tables.jl column tables and returning a matrix laid out like the target (its rows and columns in the target's order, without the label column). Replace the stub Pollis wrote. Use well-known registered packages and list them in `packages` in method.toml; Tables.jl for tables, never DataFrames; Enzyme rather than Zygote when the method needs reverse-mode derivatives. Give every exported function a docstring that says what it computes, what it takes and what it returns, and which table of the article it reproduces.
4. **Write the calls**: for each target, `call` in its `[targets.NAME]` table, the Julia expression that computes it from the inputs (each input a variable named after its `[inputs.NAME]` table). Write the calls that are missing; change one the user wrote only if it cannot work, and say why. Leave `tolerance` at 0 unless the user agrees to raise it (step 6).
5. **Run the test command** and read the output. A failure names the target (its `source`), the row and the column. Fix the code, not the targets: a target is what the article printed. If a value in a target looks mistyped (a digit swapped compared with the article), show the user both and let them correct it.
6. **When values stay off**, compare yours with the printed ones, value by value, rounded to the digits printed: say how far each is, in units of the last digit. Look for the usual causes first: the sample (missing values, the years used), the degrees of freedom, the variance estimator, the starting values or the optimiser of an iterative estimate, the random seed of a simulation. If the article cannot be reproduced exactly (a value off by one in the last digit after all of these, say), tell the user which values, by how much, and what you checked, and let them decide whether to raise that target's `tolerance`.
7. **Add a usage example** to method.toml: `usage = """..."""`, Julia code that loads the data and calls the method, as a user would. It goes in the package's README.
8. **Report**: for each target, the values reproduced and those not, with the values side by side (yours, rounded to the digits printed, and the article's), never just "identical" or "matches"; the tolerance of each target; what you assumed where the article was unclear. Then the next steps: **Compose > New Methods > Prepare for Registration...** if the test output lists registry rules still broken, and **Compose > New Methods > Package and Publish...** to publish.

## Rules

- Edit only the method folder: `method.toml`, `src/`, and `targets/` only to fix a value the user confirmed was mistyped. Never edit the package the test command makes (it is rewritten each run), and never add `article_local` or `local`.
- Never type in a result: every value the tests check is computed by the code from the data.
- Never use DataFrames. Never name a university in what you write.
