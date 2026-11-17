# case.toml

Every field Pollis reads, as Host a Game checks it. A field not listed here is ignored. Paths of briefings are files of the case's folder.

## The case

| Field | What it is |
|---|---|
| `title` | The case's title (required) |
| `description` | One or two sentences, shown on its card in Host a Game |
| `rounds` | The number of rounds, 1 to 50 (required) |
| `round` | What a round is called, such as `"Year"` or `"Quarter"`; `"Round"` when missing |
| `round_names` | A name for each round, such as `["Due Diligence", "Offers"]`, in place of "Year 2" |
| `round_choices` | Numbers of rounds the facilitator can choose from, each at most `rounds`; the model reads the number chosen as the parameter `rounds` |
| `teams` | `[least, most]`, the bounds on the number of teams; not used with `[[sides]]` |
| `team_names` | Names of the teams, in order; a team without one is "Team n", or its side's label |
| `briefing` | The briefing every player reads: a Markdown file, or a list of them read one after the other; at most 20,000 characters in all |
| `debrief` | A Markdown file of questions for the class, shown in the debrief |
| `negotiations` | `false` to stop teams writing to each other; on when missing, the facilitator reading every message |
| `packages` | Julia packages `model.jl` needs beyond Random, Statistics, LinearAlgebra and Printf, installed on the facilitator's computer when missing |

## [[sides]]: teams that differ

When the teams are of different kinds, such as one buyer and three sellers; the number of teams is then the sum of the sides' teams, numbered one side after the other.

| Field | What it is |
|---|---|
| `key` | Letters, digits and `_` |
| `label` | Its name, such as `"Winery"` |
| `teams` | How many teams of this side, 1 to 20 |
| `briefing` | What the side's players read, after the case's briefing |

Decisions, roles and columns take `sides = ["key", ...]` to be for some sides only.

## [[structures]] and [[structures.roles]]: how the teams are organised

Each structure is a way the facilitator can choose; without any, every player sets every decision (Board Members).

| Field | What it is |
|---|---|
| `key`, `label`, `description` | The structure, and what it means for the players |
| `roles.key`, `roles.label` | A role, such as `"marketing"`, `"Marketing"` |
| `roles.briefing` | What players of the role read |
| `roles.decides` | Keys of the decisions the role sets; every decision when missing, none with `[]` |
| `roles.sides` | The sides that have the role, in a case with sides; every side needs a role in each structure that has roles |

## [[decisions]]

| Field | What it is |
|---|---|
| `key` | Letters, digits and `_`; what `play` reads in `decisions` |
| `label`, `help` | Its name and a sentence of help for the players |
| `kind` | `"number"` (the default) or `"choice"` |
| `min`, `max`, `step`, `unit` | For a number: its bounds (required), its step and unit |
| `options`, `labels` | For a choice: the values `play` reads, and what the players see |
| `default` | The value before a team changes it, and what Pollis' test plays |
| `rounds` | The rounds in which it is decided; every round when missing |
| `sides` | The sides that decide it |
| `rivals` | `true`: decided for each other team still in the game, read in `play` as a Dict by the other team's number as a string, such as `bid["2"]` |

A case with `[market]` may have no decisions.

## [results]

| Field | What it is |
|---|---|
| `winner` | The key of a public column whose highest value in the last round wins; in a case with sides, it ranks each side's teams among themselves |
| `[[results.public]]` | The columns every player sees: `key`, `label`, `unit`, `digits` (0 to 6) |
| `[[results.private]]` | The columns each team alone sees; `sides` for columns of some sides only |

The public rows name their team in a column `team`, with the team's name. A table of one row is shown as a column of items and values.

## [[news]]

| Field | What it is |
|---|---|
| `round`, or `rounds = [first, last]` | The round it can be released in, or a window the game draws one from (one of the two, required) |
| `chance` | Above 0 and at most 1: the chance it is in a game; every game when missing |
| `title`, `text` | What the players read; the text at most 2,000 characters |
| `roles` | Keys of the roles that read it; every player when missing |
| `[news.parameters]` | Numbers of `[parameters]` it changes from its round on, each a value or a range `[low, high]` the game draws from; a nested table changes a nested parameter |

## [parameters]

The numbers and tables `model.jl` reads as `parameters`, with the news released so far applied. A range `[low, high]` is an array of two numbers that `initial` draws from; Pollis does not draw it.

## [market]: a market by the clock

In place of a form of decisions, the teams trade one asset on an order book for a few minutes each round; `play` reads each desk's position at the close as its decisions (see the header of `game.jl`).

| Field | What it is |
|---|---|
| `asset`, `unit` | What is traded, and the currency |
| `minutes` | The length of a period, 0.5 to 60 |
| `tick` | The step of prices, above 0 |
| `cash`, `shares` | Each desk's cash and whole shares at the start |
| `max_order`, `max_price` | The most shares of one order, and the highest price, above the tick |
