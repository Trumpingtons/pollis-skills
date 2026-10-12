---
name: write-vignette
description: "Write the vignette of a Pollis method: a two-page LaTeX document, in the method folder, that says what the method does, which article it comes from, and how close its tests come to the published values, side by side. Triggers include 'write a vignette', 'document this method', 'write the vignette of this method', 'Write a Vignette'. Run it once the write-method skill has the targets passing."
---

# Write a Vignette

Writes the vignette of a Pollis method: about two pages that a reader can trust without running anything. What the article asked, what the method computes, every value the tests check beside the published one, what is still open, and the references. The method's folder was made in Pollis (**Compose > New Methods > Create Method...**) and its code written with the write-method skill; read `../write-method/reference.md` for the method folder, method.toml and the test command.

The vignette is `vignette/NAME.tex` in the method folder (NAME is the method's `name`), with its PDF beside it. **Compose > New Methods > Package and Publish...** puts it in the package.

## What you are given

The first message from Pollis names the method folder and gives the test command, with its paths. If it does not (the skill was started some other way):

- **The folder**: the current folder if it has a `method.toml`; else list the folders of `~/.pollis/methods` that have one, and ask which.
- **The test command**: ask the user to start this task from Pollis instead, with **Compose > New Methods > Write a Vignette...**, which gives it.

Next to this SKILL.md are `vignette_style.tex` (the look: fonts, colours, the macros below) and `template.tex` (the sections, to fill in).

## Steps

1. **Read the method**: `method.toml`, `INSTRUCTIONS.md`, `src/NAME.jl` and every `targets/*.csv`. Read the article from its link (`article`, else `https://doi.org/` and the `doi`). Do not save a copy of the article or the data.
2. **Run the test command.** If a target fails because the code is wrong or missing, stop: tell the user to finish the method first (**Compose > New Methods > Write a Method...**). A vignette describes a tested method; it is never written before the tests run.
3. **Get the values**: the test command prints `Packaging NAME in FOLDER`; its `test/runtests.jl` shows how the inputs are read. In that package's environment, evaluate each target's `call` and round each value to the digits the article printed. These are the Julia column of the value table: never type one in, never copy one from the target as if it were computed.
4. **Write the vignette**: make `vignette/` in the method folder, copy `vignette_style.tex` into it, copy `template.tex` to `vignette/NAME.tex`, and fill it in:
   - `\vignetteauthor` from `author` in method.toml, `\vignettedate` today's date written out (12 October 2026). If method.toml has a `license`, set `\vignettelicense` to it.
   - `\nmhead{short name}{what it does, one line}{the article, reproduced}`.
   - `\factsheet{tested against}{missing in Julia}{package}{result}`: the article, its tables and pages, and the data; what no Julia package did (check the registered packages before saying so) and what the packages used do instead; `\package{NAME}{URL}` with the GitHub URL if it is published, else `\fn{NAME.jl}`; how many printed values are reproduced, to how many digits.
   - **The problem**: the question the article asked, of which data, and why the usual tools do not answer it.
   - **The method**: what it computes, in a few equations, with the article's choices (estimator, corrections, sample). Name each exported function with `\fn{...}` and say what it returns.
   - **What the tests show**: a `valuetable`, one row per value or per group of values, with the source (table and page), what is compared, the published value, Julia's, and `\ok` or `\no`. A long table becomes rows by group ("Table 3, 24 coefficients: 22 to every digit, 2 off by one in the last") with the closest and furthest values shown. Then **Where they differ**: each value that is off, by how much in units of the last digit printed, and what was checked.
   - **Still open**: what the article does that the method does not reproduce yet, and why.
   - **Further considerations**: where not to use the method, what would extend it and the published test the extension would need, and whether it could be offered to an existing package.
   - **References**: the article first. Cite in the text with `\nmcite{key}{Author (year)}` and list each work with `\nmref{key}{...}` in the style of the template: all authors, year, title, journal, volume(issue), pages, and `\nmdoi{...}` when there is a DOI. Use the references skill to look works up; never invent a DOI.
5. **Compile**, from `vignette/`:

   ```sh
   julia -e 'using PollisTeX; display(compile("NAME.tex"))'
   ```

   If PollisTeX is missing, add it first with `julia -e 'import Pkg; Pkg.add(url = "https://github.com/Trumpingtons/PollisTeX.jl")'`. It prints `PDF: ...`, then only the problems, as `error: file.tex:LINE: message`. Fix each error at its line; never open the `.log`. Compile at most five times; if errors remain, stop and list them. Delete the auxiliary files it leaves (keep only `NAME.tex`, `vignette_style.tex` and `NAME.pdf`).
6. **Keep it to two pages.** If it runs over, shorten the prose, never the value table, and compile again.
7. **Report**: where the PDF is, how many values the table shows and how many agree, and anything you could not settle from the article. Then the next step: **Compose > New Methods > Package and Publish...**.

## Macros

`\nmsection{...}` and `\nmsub{...}` for headings; `\fn{...}` for code; `\menupath{Compose \gt New Methods}` for a Pollis menu; `\begin{pbox}{Title}...\end{pbox}` for a pale box, `nbox` for a dark one (at most one box); `\ok` and `\no` in the value table. The engine is XeTeX: write `°`, `é`, `–` directly or as LaTeX, never `\usepackage{inputenc}`.

## Rules

- Write only in `vignette/` of the method folder.
- Every number in the vignette comes from the article or from the code run in step 3. Show values side by side, rounded to the digits printed, and say how close they are ("to every printed digit", "off by 2 in the third decimal"); never just "identical" or "matches".
- Plain, short sentences; no marketing words. Say what the method does not do as clearly as what it does.
- Never use DataFrames. Never name a university.
