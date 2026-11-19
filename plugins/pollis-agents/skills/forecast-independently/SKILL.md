---
name: forecast-independently
description: "Write a forecasting method on your own, as one of several agents given the same series of a forecasting competition: look at the series in Julia, write method.jl, a function forecast(y, h, period) that returns h forecasts of a series, check it on every series with Pollis' check.jl, and say what it does and why in REPORT.md, with no questions to anyone. Pollis then scores it on the values held back. Triggers include 'write a forecasting method for the series of TASK.md', 'you are one of several agents who each write a forecasting method', 'Agents' Forecasting Competition'."
metadata:
  updated: "2026-11-14"
---

# Forecast Independently

You are one of several agents of a forecasting competition, started by Pollis from **Compose > Prediction Competitions > Agents' Forecasting Competition...**. Every agent gets the same series, the values a competition's forecasters saw, and each writes, alone, the forecasting method it believes will forecast them best. Pollis then runs every method, scores its forecasts on the values that came after, which no agent has, as the competition's organisers scored its entries, and compares the methods with each other, with their combinations and with the competition's benchmarks.

Nobody will answer questions while you work: decide for yourself, and say in `REPORT.md` what you decided and why.

## What you are given

Your folder, the one you start in, holds:

- **`TASK.md`**: the competition, the frequency, the number of series, how far ahead to forecast, and the Julia command to run.
- **`SKILL.md`**: this file.
- **`data/series.csv`**: the series, a row for each value: `series` (its name), `period` (its seasonal period, 1 for none), `horizon` (how many values ahead to forecast), `t` (the value's place, from 1) and `value`. **`data/README.md`** says the same.
- **`check.jl`**: written by Pollis. It runs your method on every series as Pollis will, and prints each series it fails on and the time it took. Do not change it.

## Rules

- **Stay in your folder.** Write files only there. Read nothing outside it. Do not look for other agents' folders.
- **Never look at the values held back.** Do not read `~/.pollis/competitions`, and do not call the data functions of PollisCompetitions (`series`, `observations`, `forecasts`, `scores`, `published_scores`, `download`...): they hold the values your forecasts are scored on and the entries' forecasts. You may use the forecasting functions of `PollisCompetitions.M4Benchmarks` (`ses`, `holt`, `theta`, `is_seasonal`, `seasonal_index`), which forecast from the values you give them.
- **Do not look up the competition's results**, its entries' forecasts or their scores. You may build on any forecasting method you know, and say so in `REPORT.md`.
- **Run commands only with `julia`**, as `TASK.md` shows; read and write files with your own tools.
- **Never make up a number.** If the method cannot be made to work, write `REPORT.md` saying what failed, and stop.

## What you write

1. **`method.jl`**, which defines one function:

   ```julia
   Pollis.packages("StatsBase")   # every package it uses beyond Julia's standard libraries
   using StatsBase
   using PollisCompetitions.M4Benchmarks: is_seasonal, seasonal_index, theta

   """
   h forecasts of the series y (its values in order, a Vector{Float64}), whose seasonal period is period (1 for none).
   """
   function forecast(y, h, period)
       ...
       return f   # h finite numbers
   end
   ```

   Pollis includes `method.jl` in a module of its own, where `Pollis` is defined, then calls `forecast` once for each series. So:
   - `forecast` returns exactly `h` finite numbers for **every** series: short ones, constant ones, ones with zeros, ones with a period longer than the series. Fall back to a simpler method when a better one cannot be fitted, rather than throw an error: a series that fails leaves your method unscored.
   - It is **called from several threads at once**: it changes no global variable and sets no global seed. Where it draws at random, it makes a local generator, such as `Random.Xoshiro(1)`.
   - It reads no file: everything it needs is `y`, `h` and `period`.
   - It is **fast enough** that `check.jl` forecasts every series in a few minutes on this computer: the scoring runs it again on every series.
2. **`REPORT.md`**: half a page. The method, and why you chose it for these series; what you tried and dropped; how you checked it, such as holding back the last `h` values of each series you have and comparing your method with simpler ones on them; and the time `check.jl` took.

## Steps

1. Read `TASK.md` and `data/README.md`. Look at the series with Julia, in a file of your own: their lengths, levels, trends, seasonality, zeros and outliers.
2. Choose a method. To compare candidates, hold back the last `h` values of each series you have, forecast them from the values before, and measure the errors; never use anything but `data/series.csv` for it.
3. Write `method.jl`, and run `check.jl` with the command of `TASK.md` until every series is forecast.
4. Write `REPORT.md`.
5. Stop, with a one-paragraph summary: the method, how it did on your own check, and the time `check.jl` took.
