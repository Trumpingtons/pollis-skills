---
name: polish-report
description: "Polish a LaTeX report or slides that Pollis wrote from a notebook: a title and abstract, sections that follow the notebook, booktabs tables and captioned figures built only from the outputs Pollis saved, published values beside Julia's, citations from the article's BibTeX or pollis.bib, compiled with PollisTeX. Triggers include 'polish this report', 'polish the slides', 'make this report read well', 'Polish with Agent'."
---

# Polish a LaTeX Report or Slides Written by Pollis

You are in the report folder. The first message names the document, `report.tex` (class `article`) or `slides.tex`
(class `beamer`), and gives the folder of pollis.bib, the Pollis library of references. Pollis has already run the
notebook and written the document; your task is to make it read well. Pollis shows the PDF beside you and reloads it
each time it changes.

If the first message does not name the document (the skill was started some other way), ask the user to start this
task from Pollis instead: open the `.tex` that **Export Notebook as LaTeX...** or **Make Slides from a Notebook...**
wrote, then **Polish with Agent...** (**Compose > Reports**).

## What is here

- The `.tex`: a draft of the notebook's text with its outputs, or a workflow's own template.
- `notebook.md`: the notebook as short Markdown: its article, then each cell, with the code's printed output and
  figure. Read it instead of the `.ipynb`.
- `outputs/cellN.txt` and `figures/cellN.pdf`: what cell N printed and drew, or, with a template, `tables/` and
  `figures/` written by `make.jl`.

Never run the notebook, `make.jl` or other Julia code, and never change the notebook: the numbers are already here.
Never type a number by hand: every value comes from these files.

## What to do

- **Report**: a title and an abstract of a few lines; sections that follow the notebook (the question, the data, the
  method with its equations, the results, what they show); outputs as `booktabs` tables built from the values in
  `outputs/` where that reads better than the printed text; figures with captions. Leave `\author{}` for the user.
- **Slides**: a title frame, then one idea per frame, at most six lines of text; a table or figure fills its frame.
- When the notebook replicates an article, show each published value beside Julia's, rounded to the published
  digits, with the page or table it comes from, and the agreement as "same to N decimals"; never "identical".
  Cite the article from the BibTeX in `notebook.md`, written to `references.bib`, with natbib and
  `\bibliographystyle{plainnat}`. For any other work, cite it from pollis.bib, the Pollis library of references,
  with `\bibliography{references,pollis}` (see Compile). Cite as Author (year) with every author (`\citet*`), and
  never invent an entry.
- Keep the preamble's fonts and `fancyvrb`; the engine is XeTeX (`fontspec`, never `inputenc`).
- Never use DataFrames.jl.

## Compile

```sh
julia -e 'using PollisTeX; display(compile("report.tex"))'
```

When the document uses `\bibliography{…,pollis}`, give Tectonic the folder of pollis.bib that the first message
gives:

```sh
julia -e 'using PollisTeX; display(compile("report.tex"; searchpath = ["/the/folder/of/pollis.bib"]))'
```

(`slides.tex` for slides; if PollisTeX is missing, add it first with
`julia -e 'import Pkg; Pkg.add(url = "https://github.com/Trumpingtons/PollisTeX.jl")'`.) It prints `PDF: …`, then
only the problems, as `error: file.tex:LINE: message` or `warning: …`. Fix each error at its line. Read only those
lines: never open the `.log`. Compile at most five times; if errors remain, stop and list them.

## Finish

Say in one line what you changed, and which values, if any, differ from the published ones.
