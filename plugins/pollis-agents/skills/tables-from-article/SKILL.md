---
name: tables-from-article
description: "Type in tables of the article of a Pollis method or replication, as printed: target tables (the published results to reproduce) or input data, each a CSV in its folder with its entry in method.toml or replication.toml, then list every value that could not be read for certain. Triggers include 'type in the tables of the article', 'tables from the article', 'add these tables as targets', 'add these tables as input data', 'Add from Article'."
metadata:
  updated: "2026-11-10"
---

# Tables from the Article

Types in tables of the article of a Pollis method or replication, exactly as printed, so that the code can be tested
against them (target tables) or run on them (input data). You type in tables; you write no code.

## What you are given

The first message from Pollis names the folder, the tables (for example `3.2, 5.8`) and what they are: target tables
or input data. The folder holds `method.toml` (a method) or `replication.toml` (a replication): this is the folder's
record, and everything below goes in it. If the message does not say these things (the skill was started some other
way), ask the user to start this task from Pollis instead, with **Add from Article** in Article and Data, on the
page of the method (**Compose > Replication > Browse Article Methods**, then the method's title) or of the replication
(**Compose > Replication > Browse Replications**, then the replication's title).

## Steps

1. **Read the article**: its link is `article` in the record, else the page of its `doi`. Do not save a copy of it.
2. **Type in each table**, named like `table3_2` for Table 3.2:
   - **Target tables**: `targets/NAME.csv`, with the row labels in the first column and every value exactly as
     printed (the same digits and signs, never rounded or recomputed, an empty cell where the table has none), and in
     the record a `[targets.NAME]` table with `source = "Table 3.2, page 41 (typed in by a coding agent)"`. In
     `method.toml` it also has `call = ""` and `tolerance = 0`; in `replication.toml` it has only `source`.
   - **Input data**: `inputs/NAME.csv`, with a header line of column names and every value exactly as printed, and in
     the record an `[inputs.NAME]` table with `file = "inputs/NAME.csv"` and
     `source = "Table 3.2, page 41 (typed in by a coding agent)"`. In `replication.toml` it also has `licence = ""`:
     leave it empty, as the terms of the article's tables are for the user to find out.
3. **Report**: list each table you typed in, with its size, and every value you could not read for certain, so that
   the user can check them against the article.

## Rules

- Change nothing else in the folder.
- Never round, recompute or guess a value: a value you cannot read goes in the report.
