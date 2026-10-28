---
name: tables-from-article
description: "Type in tables of a Pollis method's article, as printed: target tables (the published results the method must reproduce) or input data, each a CSV in the method folder with its entry in method.toml, then list every value that could not be read for certain. Triggers include 'type in the tables of the article', 'tables from the article', 'add these tables as targets', 'add these tables as input data', 'Tables from the Article'."
---

# Tables from the Article

Types in tables of the article of a Pollis method, exactly as printed, so that the method can be tested against them
(target tables) or run on them (input data). You type in tables; you write no code.

## What you are given

The first message from Pollis names the method folder, the tables (for example `3.2, 5.8`) and what they are: target
tables or input data. If it does not (the skill was started some other way), ask the user to start this task from
Pollis instead, with **Tables from the Article** in Article and Data on the method's page (**Compose > New Methods >
Browse Methods**, then the method's title).

## Steps

1. **Read the article** of the method: its link is `article` in the folder's `method.toml`, else the page of its
   `doi`. Do not save a copy of it.
2. **Type in each table**, named like `table3_2` for Table 3.2:
   - **Target tables**: `targets/NAME.csv`, with the row labels in the first column and every value exactly as
     printed (the same digits and signs, never rounded or recomputed, an empty cell where the table has none), and in
     `method.toml` a `[targets.NAME]` table with `source = "Table 3.2, page 41 (typed in by a coding agent)"`,
     `call = ""` and `tolerance = 0`.
   - **Input data**: `inputs/NAME.csv`, with a header line of column names and every value exactly as printed, and in
     `method.toml` an `[inputs.NAME]` table with `file = "inputs/NAME.csv"` and
     `source = "Table 3.2, page 41 (typed in by a coding agent)"`.
3. **Report**: list each table you typed in, with its size, and every value you could not read for certain, so that
   the user can check them against the article.

## Rules

- Change nothing else in the method folder.
- Never round, recompute or guess a value: a value you cannot read goes in the report.
