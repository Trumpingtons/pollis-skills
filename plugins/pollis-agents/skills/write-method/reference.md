# The Pollis method format

A Pollis method is a published statistical method turned into a Julia package whose tests are the article's own tables. The user makes the method's folder on its page in Pollis (**Compose > New Methods > Create Methods**; **Browse Methods** reopens it later): they link the article and the data, type in the published tables (or ask the coding agent to, from the article) and name it. The coding agent writes the code (the `write-method` skill) and brings it up to the registry rules (the `prepare-method` skill). Package and Publish, the last step of the method's page, then makes the package, runs its tests and, when the user asks, pushes it to GitHub and registers it in the Pollis registry.

## The folder

Each method is a folder of `~/.pollis/methods`:

```
NAME/
  method.toml       the method: its name, the article, the inputs, the targets and their calls
  src/NAME.jl       the code: module NAME
  targets/*.csv     the published tables the code must reproduce, typed in as printed
  inputs/*.csv      input tables typed in from the article (the other inputs are links)
  INSTRUCTIONS.md   the user's notes for the agent: what the method does, where in the article, what to watch for
  VIGNETTE.md       the user's notes for the vignette: its look, its sections, the LaTeX style, the vignettes to follow
  vignette/NAME.tex the vignette (the write-vignette skill), its PDF beside it
```

## method.toml

```toml
name = "Grunfeld"                  # the package name: a capital letter, then letters and digits
title = "Panel regressions of Grunfeld's investment data"
vignette_title = ""                # the vignette's title, when it is not the title
author = "Ana Silva"
description = "Pooled, fixed and random effects estimates of the investment equation."
version = "0.1.0"                  # optional, default 0.1.0

citation = "Author, A. and Author, B. (1990). Title of the article. Journal, 12(3), 45-67."
doi = ""                           # without the https://doi.org/
article = "https://..."            # a link to read the article: its PDF or its web page; else the DOI's page

packages = ["Distributions"]       # the Julia packages src/NAME.jl uses (registered names)

usage = """
using Grunfeld, CSV, Tables
data = Tables.columntable(CSV.File("grunfeld.csv"))
fixed_effects(data)
"""

[inputs.grunfeld]                  # an input table: a variable named grunfeld in the calls
url = "https://raw.githubusercontent.com/..."   # or file = "inputs/grunfeld.csv", a table of the method
source = "Where the data comes from"
delim = ","                        # the default; or ";", "tab", "whitespace"
columns = ["firm", "year", "I"]    # only for a file without a header line
local = "..."                      # set by Pollis when the user saved a copy: never write it

[targets.table1]                   # one table for each targets/table1.csv
source = "Table 1, page 12"
call = "fixed_effects(grunfeld)"   # the Julia expression that computes it
tolerance = 0                      # how many units of the last printed digit the result may be off by
```

- **Inputs are links.** The tests download each input into memory, as a Tables.jl column table (`Tables.columntable` of a `CSV.File`), and bind it to a variable named after its table. Nothing is saved to disk. Never use DataFrames.
- **A target** is a CSV file: its first column holds the row labels, its other cells the published values exactly as printed (`0.0631`, `-12.4`), a cell left empty where the table has no value. Its header row names the columns.
- **A call** must return a matrix laid out like the target, without the label column: the same rows and columns, in the same order. A Tables.jl table is turned into a matrix with `Tables.matrix`; a vector is one column.
- **A value passes** when the result, rounded to the number of decimals printed, equals the printed value within `tolerance` units of its last digit. A printed `0.0631` has 4 decimals: the result `0.06312` passes, `0.06318` fails with tolerance 0 and passes with tolerance 1.
- `article_local`, and `local` in an input, are paths to copies the user chose to keep. Pollis writes them; leave them as they are.

## src/NAME.jl

```julia
module Grunfeld

using LinearAlgebra, Statistics, Tables

export fixed_effects

"""
    fixed_effects(data)

The within estimates of the investment equation, I on F and C with a fixed effect for each firm, from
a Tables.jl table with the columns firm, I, F and C: a 2 × 2 matrix, the coefficients of F and C in its
rows, their estimates and standard errors in its columns, as in Table 1.
"""
function fixed_effects(data)
	...
end

end
```

Pollis writes a stub when it makes the method,, `estimate(data)`, which errors. Replace it.

## The test command

Package and Publish runs Pollis' own script, `package.jl`:

```
julia "<Pollis>/.../media/methods/package.jl" "<packages folder>" "<method folder>"
```

Pollis gives the exact command, with its paths, in the first message when it starts the agent. Without `--push`, it pushes nothing and registers nothing: it makes the package `NAME.jl` in the packages folder (the code copied from `src/`, the targets as test data, `test/runtests.jl` written from method.toml), runs its tests, and reports the registry rules the method still breaks. It overwrites `src/` and the test data of that package each time, so always edit the method folder, never the package.

The test output has one testset per target, named by its `source`, and one test per printed value, named by its row and column label, so a failure says which value is off.

## The Pollis registry rules

A method is registered only when its tests pass and it meets all three:

1. **A name of at least 5 characters**: `name` in method.toml, the module, the file `src/NAME.jl` and the method folder all match.
2. **A usage example**: `usage = """..."""` in method.toml, Julia code that calls the method on its data. It goes in the package's README.
3. **A docstring for every exported name** of the module.
