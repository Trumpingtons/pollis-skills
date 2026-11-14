# The Pollis method format

A Pollis method is a published statistical method turned into a Julia package whose tests are the article's own tables. The user makes the method's folder on its page in Pollis (**Compose > Article Replication > Create Article Method**; **Browse Article Methods** reopens it later): they link the article and the data, type in the published tables (or ask the coding agent to, from the article) and name it. The coding agent writes the code (the `write-method` skill) and brings it up to the registry rules (the `prepare-method` skill). Package and Publish, the last step of the method's page, then makes the package, runs its tests and, when the user asks, pushes it to GitHub and registers it in the Pollis registry.

## The folder

Each method is a folder of `~/.pollis/methods`:

```
NAME/
  method.toml       the method: its name, the article, the inputs, the targets and their calls
  src/NAME.jl       the code: module NAME
  targets/*.csv     the published tables the code must reproduce, typed in as printed
  inputs/*.csv      input tables typed in from the article (the other inputs are links)
  INSTRUCTIONS.md   the user's notes for the agent: what the method does, where in the article, what to watch for
  PROGRESS.md       the coding agent's record of its work: what the article settles, where each target stands, the next step
  tests/tests.jl    the reference, property and convergence tests, which the package's tests include ([tests] in method.toml)
  tests/references/ the saved results the reference tests compare with, one file to a result
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
untested = [["mu", "Standard error"]]   # optional: cells shown in the vignette but not tested, as [row label, column]

[tests]                            # the reference, property and convergence tests (see Reference and property tests below)
file = "tests/tests.jl"
copy = ["tests/references"]
packages = ["ReferenceTests", "Supposition", "StableRNGs", "Random"]   # and every package tests.jl loads beyond Test, CSV, Downloads and Tables

[[articles]]                       # optional, after every other top-level key: another article whose tables are targets too
key = "Key2014a"
citation = "Author, C. (2014). Title. Journal, 82(2), 765-784."
doi = ""
article = "https://..."
```

- **Inputs are links.** The tests download each input into memory, as a Tables.jl column table (`Tables.columntable` of a `CSV.File`), and bind it to a variable named after its table. Nothing is saved to disk. Never use DataFrames.
- **A target** is a CSV file: its first column holds the row labels, its other cells the published values exactly as printed (`0.0631`, `-12.4`), a cell left empty where the table has no value. Its header row names the columns.
- **A call** must return a matrix laid out like the target, without the label column: the same rows and columns, in the same order. A Tables.jl table is turned into a matrix with `Tables.matrix`; a vector is one column.
- **A value passes** when the result, rounded to the number of decimals printed, equals the printed value within `tolerance` units of its last digit. A printed `0.0631` has 4 decimals: the result `0.06312` passes, `0.06318` fails with tolerance 0 and passes with tolerance 1.
- **Tolerance** is 0 for values the article computed exactly and 3 for simulated ones (bootstrap or Monte Carlo p-values and critical values), unless the user decides otherwise. A value that stays off for a reason found and explained (a numerical derivative of the authors' software, random draws that cannot be reproduced) keeps its printed value in the target and goes in `untested`, with the reason, for the vignette; never blank the cell, and never raise the tolerance for it.
- **Several articles**: when one package reproduces more than one article, the first is the method's own (`key`, `citation`, `doi`, `article`), the others are `[[articles]]` tables, and each target's name and `source` start with its article.
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

## Reference and property tests

The targets check what the article printed, to the digits printed. More tests, in `tests/tests.jl`, guard the rest once the targets pass:

- **Reference tests** (ReferenceTests.jl) save the method's results in full, once they are right, in `tests/references/`, and fail when a later run gives other results: a change to the code, or a new version of a package the method uses, that moves a digit the article did not print, a standard error it did not report or a value the targets do not check.
- **Property tests** (Supposition.jl) try a rule the method's results must obey on inputs it generates, such as estimates that do not depend on the order of the rows, and print the smallest input that breaks it.
- **Convergence tests**, only for a method that steps a differential equation with a scheme of its own, check that the scheme's error falls at its order as the step is halved.

The package's `test/runtests.jl` includes `tests/tests.jl` inside its testset, after it has defined `input(source)`, which loads an input from its link or file as the targets do, and `using NAME`. A path given to `@test_reference` is relative to `tests/tests.jl`, so `"references/model32.txt"` is `tests/references/model32.txt`.

```julia
# The reference and property tests of Grunfeld, which the package's test/runtests.jl includes ([tests] in method.toml)
using ReferenceTests, Supposition, StableRNGs, Random

"A result in full, one number to a line."
in_full(result) = join(string.(vec(result)), "\n")
"Two results in full, the same within a relative tolerance."
within(rtol) = (reference, actual) -> isapprox(parse.(Float64, split(reference)), parse.(Float64, split(actual)); rtol)

grunfeld = input("https://raw.githubusercontent.com/...")   # the input's url or file, as in [inputs.grunfeld]

@testset "Reference tests" begin
	# The fixed effects estimates in full, which the target table1 checks to the digits printed
	@test_reference "references/fixed_effects.txt" in_full(fixed_effects(grunfeld)) by=within(1e-8)
end

@testset "Property tests" begin
	n = length(grunfeld.firm)
	original = fixed_effects(grunfeld)
	# The within estimator does not depend on the order of the observations
	@check db=false rng=Xoshiro(1) max_examples=20 function row_order(seed = Data.Integers(1, 10_000))
		order = randperm(StableRNG(seed), n)
		isapprox(fixed_effects(map(column -> column[order], grunfeld)), original; rtol = 1e-8)
	end
end
```

**What to save.** The result of each target's call, in full, and the method's other results that a user gets but the targets do not check (every case-deletion statistic, not only the four the article printed; the whole curve, not only its printed points). Numbers in full, one to a line, with `in_full`: a text file a reader can compare line by line.

**The tolerance** of `within`: wider than the differences in the last digits that another processor or another version of a linear algebra library gives, and much narrower than the last digit the article printed. `1e-8` for a result computed in closed form or by a converged iteration; the iteration's own tolerance when it stops before full precision (an optimiser's `g_tol`, say). Say in a comment why, when it is not `1e-8`.

**Random results** (a bootstrap, a simulation) change when the random numbers change, and the stream of Julia's default generators (`Xoshiro`, `MersenneTwister`, `Random.default_rng()`) may change with a minor release of Julia. Save them only when the method's function takes an `rng` keyword, and pass it a `StableRNG(seed)` of StableRNGs.jl, whose stream stays the same across versions. A random result whose function takes no `rng` gets no reference test: its target, with its tolerance, checks it.

**Properties** follow from the method, never from its results: say in a comment why each must hold. One to three are enough, for instance:

- the result does not depend on the order of the observations (most estimators; not a time series model, whose order is its time);
- a result changes as the method says it must when the data are transformed: estimates scale with a rescaled variable, an intercept takes a constant added to the response;
- a result stays within its bounds: a probability between 0 and 1, a variance or a standard error not negative, a lower confidence bound below the upper.

Generate what the property varies (a seed for a permutation, a scale, a temperature), not whole data sets: the method's own inputs are the data. Every `@check` takes `db=false`, so that it writes no database of counterexamples into the package, and `rng=Xoshiro(1)`; keep `max_examples` small (10 to 50), as each example runs the method. A property that fails is a finding: report it, and never weaken the property to make it pass.

**Convergence tests**, only for a method whose `src/` steps a differential equation forward in time with a scheme of its own: a loop over the time steps of a diffusion (an Euler-Maruyama or Milstein simulation), a discretised model in continuous time, an ODE solver written by hand. A method that calls the solvers of DifferentialEquations.jl, or has no differential equation, gets none: its solvers are tested in their own packages. A convergence test runs the scheme at step sizes each half the one before, on a problem whose exact solution is known, and checks that the error falls at the scheme's order: the slope of log(error) against log(step).

```julia
using Statistics   # cov and var; add "Statistics" to packages in [tests]

@testset "Convergence tests" begin
	# The strong order of the method's Milstein step, 1 (the article's section 3), on geometric Brownian motion,
	# dX = mu X dt + sigma X dW, whose exact solution on each path is exp((mu - sigma^2 / 2) t + sigma W(t))
	mu, sigma = 2.0, 1.0
	step(x, dt, dW) = NAME.milstein_step(x, dt, dW; drift = x -> mu * x, diffusion = x -> sigma * x)   # the method's own step
	dts = 2.0 .^ (-4:-1:-9)
	finest, n, paths = minimum(dts), 2^9, 1000
	rng = StableRNG(1)
	errors = zeros(length(dts))
	for _ in 1:paths
		dW = sqrt(finest) .* randn(rng, n)   # one path, at the smallest step
		exact_end = exp((mu - sigma^2 / 2) + sigma * sum(dW))
		for (j, dt) in enumerate(dts)
			r = round(Int, dt / finest)   # each increment of a larger step is the sum of the r small ones it covers
			x = 1.0
			for k in 1:div(n, r)
				x = step(x, dt, sum(view(dW, ((k - 1) * r + 1):(k * r))))
			end
			errors[j] += abs(x - exact_end) / paths
		end
	end
	@test abs(cov(log.(dts), log.(errors)) / var(log.(dts)) - 1.0) <= 0.2
end
```

- **The order** is the one the article gives for its scheme or, when it gives none, the scheme's own: Euler 1 for an ODE; Euler-Maruyama strong 1/2 and weak 1, Milstein strong 1. Say in a comment where it comes from.
- **The problem**: one with an exact solution, run through the method's own step: geometric Brownian motion or a linear ODE when the step takes its drift and diffusion as arguments, or the method's own model at parameters for which it has an exact solution. When neither works, write no convergence test and say why in the report.
- **An ODE scheme** needs no paths: solve at each step and take the distance from the exact solution at the end, as the inner loop of the template does for one path.
- **The same paths at every step**: draw the Brownian increments once, at the smallest step, with a `StableRNG`, and sum them for each larger step, as in the template. Paths drawn afresh for each step put the difference between paths into the error.
- **Steps and paths**: from 2^-4 to 2^-9, and 1000 paths for a strong order. Test the strong order unless the article states only a weak one: a weak error is smaller, and needs about 10 000 paths and the means of the scheme and of the exact solution compared on the same paths.
- **The gap**: 0.2. Before writing it, run the test at seeds 1 to 5 and check that every estimate lies within it; if not, add paths, never widen the gap to make it pass. An order that falls short is a finding: report it.
- Keep the test to a few seconds.

**Saving the references.** ReferenceTests saves a reference that does not exist yet, and passes, rather than failing; the test command saves it in the package it makes, which it rewrites on the next run. So:

1. Make the folder `tests/references` (empty), write `tests/tests.jl` and the `[tests]` table, and run the test command. Its output says, for each reference, *Reference file for "..." did not exist. It has been created*, with its content and its path in the package (`test/tests/references/`).
2. Check each saved reference against the result it should hold, then copy the files into the method folder's `tests/references/`.
3. Run the test command again: it must pass with no *did not exist* in its output.

A reference test that fails later says *Reference Test for "..." failed*, with the reference and the new result. Find the cause before anything else; replace a reference only when the new result is known to be right (a corrected method, a deliberate change), by deleting the file in `tests/references/` and saving it again as in steps 1 to 3.

If `[tests]` already names a file (cross-tests, say), keep it: add `"tests/tests.jl"` and `"tests/references"` to `copy`, include `tests/tests.jl` at the end of that file with the path from it (`include("../tests/tests.jl")` from `crosstest/tests.jl`), and add the packages to `packages`.

## The Pollis registry rules

A method is registered only when its tests pass and it meets all three:

1. **A name of at least 5 characters**: `name` in method.toml, the module, the file `src/NAME.jl` and the method folder all match.
2. **A usage example**: `usage = """..."""` in method.toml, Julia code that calls the method on its data. It goes in the package's README.
3. **A docstring for every exported name** of the module.
