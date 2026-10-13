---
name: method-from-notebook
description: "Make a Pollis method from a notebook that already holds a published statistical method and its checks against the article: the method folder Pollis made with Method from Notebook (Compose > New Methods), with the notebook copied into its notebook/ folder. Moves the method's code into src/NAME.jl, the article's printed values into the targets, and tests it with Pollis' package script. Triggers include 'method from notebook', 'make a method from this notebook', 'turn this notebook into a method', 'package this notebook as a method'. The prepare-method skill then brings it up to the Pollis registry rules."
---

# Method from Notebook

Makes a Pollis method out of a notebook: a notebook that already implements a published statistical method and checks it against the article's tables, such as the notebooks of the New Methods library. Pollis has made the method folder (**Compose > New Methods > Method from Notebook...**), named after the notebook, titled with its first heading, and copied the notebook into its `notebook/` folder. You fill the folder so that the method's own tests are the article's tables, as for any method written with the write-method skill.

Read `../write-method/reference.md` before anything else: the method folder, method.toml, the target tables, the test command and the registry rules.

## What you are given

The first message from Pollis names the method folder, the notebook and the test command, with its paths. If it does not (the skill was started some other way):

- **The folder**: the current folder if it has a `method.toml` and a `notebook/` folder; else list the folders of `~/.pollis/methods` that have both, and ask which.
- **The test command**: ask the user to start this task from Pollis instead (**Compose > New Methods > Method from Notebook...**), which gives it. Do not look for Pollis' script yourself.

It needs Julia (`julia --version`) and the internet, to read the article and download the data. If Julia is missing, tell the user to install it with juliaup and stop.

## Steps

1. **Read the notebook**, all of it: the article it reproduces, the data it loads and from where, the functions it defines, the values it prints, and the published values it compares them with. Read `method.toml` and `INSTRUCTIONS.md` (the user's notes win over your reading). Read the article from its link, to check the notebook's published values against it. **Do not save copies** of the article or the data.
2. **Say what you found** in a few lines before you write anything: the article, the method, the tables the notebook checks, which of them are the method's own results (they become targets) and which only illustrate it, and the inputs. Ask about anything you cannot settle.
3. **Fill method.toml**: the article (`article` link and `doi`), `description`, `author` only if the user gives it, and one `[inputs.NAME]` table for each data set, as a link (`url`) to where the notebook downloads it, or typed in as `inputs/NAME.csv` when the notebook types it in from the article.
4. **Write the targets**: one `targets/NAME.csv` for each table the notebook checks against the article, with the values **as printed in the article** (the digits printed, not the notebook's own results), and its `[targets.NAME]` table with `source` (the table or page of the article). When the notebook's published value and the article disagree, show the user both and follow the article.
5. **Write `src/NAME.jl`** (module NAME) from the notebook's functions: one exported function for each thing the method computes, taking the inputs as Tables.jl column tables and returning a matrix laid out like its target, with docstrings as in the write-method skill. Keep the notebook's numerics (estimator, options, seeds) unless they cannot work in a package. List the packages in `packages`. Never DataFrames; Enzyme rather than Zygote for reverse-mode derivatives.
6. **Write the calls** for each target, and leave `tolerance` at 0. Where the notebook compares a simulation with the article (simulation noise, so no exact match is expected), say so, show the values, and let the user decide whether to raise that target's `tolerance`; never raise it yourself.
7. **Run the test command**, read the output and fix the code, as in steps 5 and 6 of the write-method skill.
8. **Add a usage example** (`usage = """..."""` in method.toml), from the notebook's own example where it has one.
9. **The name**: the method was named after the notebook. Registry names need at least 5 characters and a name that says what the method does; if this one does not, suggest a better one and tell the user to rename the method on its page (never rename the folder yourself).
10. **Report**: for each target, the values side by side (yours, rounded to the digits printed, and the article's), never just "identical" or "matches"; the tolerance of each target; what the notebook did that the method leaves out. Then the next steps, on the method's page: **Write a Vignette**, **Prepare for Registration** if the test output lists registry rules still broken, and **Package and Publish**.

## Rules

- Edit only the method folder: `method.toml`, `src/`, `targets/`, `inputs/`. Never edit `notebook/` (it is the user's copy of the original) nor the package the test command makes.
- Never type in a result: every value the tests check is computed by the code from the data.
- Never use DataFrames. Never name a university in what you write.
