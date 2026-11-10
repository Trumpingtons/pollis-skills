---
name: prepare-method
description: "Prepare a Pollis method for registration: bring a method whose code is written (with the write-method skill or by hand) up to the Pollis registry rules (a name of at least 5 characters, a usage example, a docstring for every exported name) without changing what it computes, checked with Pollis' package script. Triggers include 'prepare for registration', 'prepare this method for the registry', 'make this method meet the registry rules', 'fix the registry rules', 'add docstrings and a usage example to this method', 'Prepare for Registration'."
metadata:
  updated: "2026-11-10"
---

# Prepare a Method for Registration

Brings a Pollis method up to the Pollis registry rules, so that Package and Publish, on the method's page, can register it. The method already works: its code is written and its tests pass, or nearly. You change its name, its docstrings and its usage example, and add the reference and property tests it lacks, **never what it computes**.

Read `reference.md` in the `write-method` skill, next to this skill's folder (`../write-method/reference.md` from this SKILL.md), before anything else: the method folder, method.toml, the test command, the reference and property tests, and the registry rules.

## What you are given

The first message from Pollis names the method folder and gives the test command, with its paths. If it does not (the skill was started some other way):

- **The folder**: the current folder if it has a `method.toml`; else list the folders of `~/.pollis/methods` that have one, and ask which.
- **The test command**: ask the user to start this task from Pollis instead, with **Prepare for Registration** on the method's page (**Compose > Replication > Browse Article Methods**, then the method's title), which gives it. Do not look for Pollis' script yourself.

It needs Julia (`julia --version`). If Julia is missing, tell the user to install it with juliaup and stop.

## Steps

1. **Run the test command first** and read its last lines: the tests' result, then either "meets the Pollis registry rules" or the rules still broken. If tests fail, say which, and tell the user that the code comes first: **Write Method**, on the method's page. Go on with the rules only if the user wants to.
2. **The name**: at least 5 characters, a capital letter then letters and digits. It appears in four places that must all match: `name` in method.toml, `module NAME` in the code, the file `src/NAME.jl` and the method folder in `~/.pollis/methods`. If the name is too short or the four differ, propose a name (a word from the method or the article, not an abbreviation of 4 letters or fewer) and **ask before renaming**. Then change all four; the folder last, and tell the user Pollis shows the method under its new folder.
3. **The usage example**: if `usage` is missing or empty in method.toml, write `usage = """..."""`, Julia code a user would run: load the package and the data (from the input's link, as a Tables.jl column table, never DataFrames), call the method, and say in a comment what the result is. Keep an example the user wrote; fix it only if it calls a function that does not exist.
4. **The docstrings**: every name the module exports needs one. For a function, its signature on the first line, indented, then what it computes, what it takes and what it returns, and the table of the article it reproduces when it does. For a constant or a type, what it holds. Write them from the code and the article, not from the function's name. Do not export names only to document them, and do not stop exporting a name to avoid documenting it: ask the user if a name looks exported by mistake.
5. **The reference and property tests**: if method.toml has no `[tests]` table with `tests/tests.jl`, or `tests/references/` is empty, add them as `reference.md` describes under *Reference and property tests*, and save the references as it says (run, check, copy into `tests/references/`, run again). If `src/` steps a differential equation with a scheme of its own and the tests have no convergence test, add one as `reference.md` describes under *Convergence tests*. They test the method; they change nothing it computes. Keep the tests the method has, and never replace a saved reference: a reference test that fails means the results have changed since they were saved, which you report.
6. **Run the test command again**. The target tests must give the same result as in step 1 (you changed no computation), the reference, property and convergence tests must pass with no reference saved, and the output must say the method meets the Pollis registry rules. If a rule is still broken, fix it and run again.
7. **Report**: what you changed for each rule (the old and the new name if it changed, the usage example, the names you documented), the reference, property and convergence tests you added (what each reference saves, each property and why it holds, the order expected and the order estimated), and the last lines of the test output. Then the next step: **Package, Test, Push and Register**, in Package and Publish on the method's page.

## Rules

- Never change what the method computes: no change to a function body, a call, a target or a tolerance. If you see a bug, or a property or convergence test finds one, report it and suggest **Write Method**; do not fix it here.
- Edit only the method folder: method.toml, `src/` for names and docstrings, and `tests/`. Never edit the package the test command makes (it is rewritten each run), and never add `article_local` or `local`.
- Never use DataFrames. Never name a university in what you write.
