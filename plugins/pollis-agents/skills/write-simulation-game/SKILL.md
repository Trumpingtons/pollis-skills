---
name: write-simulation-game
description: "Write a case of Pollis' Simulation Games with the user: a folder with case.toml (the rounds, teams, decisions, results, briefings, news and parameters) and model.jl (the Julia functions initial and play), designed with the user first, then written, every round played with Pollis' game.jl until none fails; or fix the round that failed in Pollis' test; or package the case as a toolbox of cases, a .vsix file others install. Triggers include 'write a simulation game', 'write the case in this folder', 'change this case', 'fix this case', 'package this case as a toolbox', 'Write with Agent', 'Fix with Agent', 'Package with Agent'."
metadata:
  updated: "2026-11-17"
---

# Write a Simulation Game

Writes a case of **Compose > Simulation Games** with the user. A case is a folder with `case.toml`, which says what the players see and decide, and `model.jl`, the Julia model that plays each round on the facilitator's computer; the players read Markdown briefings beside the chat. The user made the folder on the page **Compose > Simulation Games > Create a Simulation Game...**, as a copy of the minimal case Sell One Product or of one of the five cases of Pollis, and tests it there with **Test the Case**.

## What you are given

The first message from Pollis names the case's folder (you start in it) and Pollis' `game.jl`, which plays one round of a case. Read the header of `game.jl` first, whole: it says what `initial` and `play` are given and what they return. Then it asks for one of three jobs:

- **Write or change the case**, with what the user said it is to be, or with nothing, in which case ask the user what the game is to be, or what to change, before anything else.
- **Fix the case**: Pollis' test failed, and the message gives the round, the model's error and the test game's folder, whose `round-NN/` folders hold what the model was given.
- **Package the case** as a toolbox of cases: see **Package a case as a toolbox** below.

If the message names no folder (the skill was started some other way), ask the user for the case's folder, or to start from the Create a Simulation Game page.

Read `reference.md`, beside this file, for every field of `case.toml`; read the files of the case's folder, which may be a copy of a case of Pollis whose design you keep where the user does not change it.

## Rules

- **Original cases only.** Never copy the text, figures, names or teaching notes of a published or licensed teaching case, a commercial simulation or a board game, even when the user supplies them: a case may take a kind of situation (a price war, an auction, a takeover) and is written anew, with its own names and numbers. Say so if the user asks for a copy.
- **What the players must not know is drawn per game.** Anyone can open `model.jl` (it is shown in the debrief), so a hidden value fixed in the file is no secret: give it as a range `[low, high]` in `[parameters]`, draw it in `initial` from the `rng`, keep it in the state, and say in the briefings only the range. Write in the `debrief` result of `initial` what was drawn, and in a round's `debrief` what it kept hidden (how far apart two teams' prices were, say), for the class afterwards.
- **News varies too.** A `[[news]]` item can have a window of rounds, `rounds = [first, last]`, a `chance`, and changes given as ranges; the facilitator releases what the game drew.
- **The state holds only what TOML can hold**: numbers, strings, Booleans, arrays and tables with string keys. No `nothing`, no `missing`, no symbols, no tuples.
- **Every key of a results row is a column of `case.toml`**, and every column is in every row: a missing one shows empty, an extra one is seen by no player.
- **Defaults play.** Every decision's `default` must give a sensible round: Pollis' test plays every round with them.
- `model.jl` uses Random, Statistics, LinearAlgebra and Printf, which Pollis loads; any other package goes in `packages = [...]` at the top of `case.toml`, and only with the user's agreement, as each facilitator's Julia installs it.
- Write for the players: the briefings in plain words, with the numbers a team needs to decide; the labels of decisions and columns in title-style capitalization.

## Steps

1. **Design, and wait.** Before writing anything, propose to the user, most important first:
   - the situation and the teams: what each team runs, how many teams (`teams = [least, most]`, or `[[sides]]` when teams differ, such as a buyer and sellers), and the ways of organising them (`[[structures]]`, with roles that set some decisions each);
   - the rounds: how many, what a round is called, and their names when they differ (`round_names`);
   - the decisions of each round, with their bounds and defaults;
   - what happens in a round: the model, as equations or rules, and which numbers are drawn per game;
   - the results everyone sees and each team alone sees, and the column that wins;
   - the news, the negotiations, and the debrief questions.

   The case's icon is the user's to choose, with **Choose an Image...** in the Write step of Create a Simulation Game; keep `icon` in `case.toml` and its file as they are.

   Then ask whether to go ahead, and write nothing until the user agrees; change the design as they ask.
2. **Write the case**: `case.toml` (comments above each part saying what it is for, as the cases of Pollis have), `model.jl` (a comment at the top with the model's equations and what is drawn per game, a docstring on `initial` and `play`), `briefing.md` (the game, the rules and how a team wins) and the briefings of the roles or sides in `briefings/`, and `debrief-questions.md` (questions for the class, the last one sending them to `model.jl`). Keep the title the user gave in `case.toml`.
3. **Play every round**, in a game folder of your own outside the case's folder (a new temporary folder), as Pollis does:
   - `game.toml` with `case`, the case folder's name, `case_folder`, its full path, `title`, `structure` (the key of the first structure, or `"board"` when there is none), `rounds`, a whole `seed`, and a `[[teams]]` table for each team, with `team` (from 1), `name` and, in a case with sides, `side`; with `round_choices`, also `[parameters]` with `rounds`;
   - `julia --startup-file=no GAME_JL CASE_FOLDER GAME_FOLDER 0`, then for each round k a `round-0k/decisions.toml` with `round = k` and a `[[teams]]` table for each team with `team`, `name` and `[teams.decisions]`, and `julia ... k`. A news item released in round k goes in `round-0k/news.toml` before the round is played;
   - after each run, `round-NN/error.txt` says why the round failed; otherwise read `results.json` and check its rows against the columns of `case.toml`.

   Play with the defaults, then with decisions at their bounds and with each news item released, and with the fewest and the most teams. A round that fails, or a result that makes no sense (a negative number of sales, a share above 100%), is fixed before you go on.
4. **Report**: what the case is, the files written, the rounds played and with which decisions, what you could not check, and that the user can now press **Test the Case** in step 3 of Create a Simulation Game, which plays it as a class would and opens the debrief. Delete your game folder.

## Fix a case

Read the error and the round's folder of the test game: `decisions.toml` (the defaults the test played), `news.toml`, and the state the round started from, `state.toml` of the round before. Find the cause in `model.jl` or `case.toml`, tell the user what it is and what you change, change it, and play every round again as in step 3 of **Steps**. Change nothing else.

## Package a case as a toolbox

A toolbox of cases is a Pollis toolbox with no panels, which others install from a `.vsix` file with **Extensions: Install from VSIX...**; its cases are then listed in Host a Game. Ask the user first for anything the message does not give: the toolbox's name (`pollis-toolbox-` followed by the case's folder name, by default), its display name (the case's title), a one-sentence description, the publisher to show, and the licence. Then:

1. Make the toolbox's folder, `toolboxes/<name>/` of the Pollis Workspace when the case is in one (the folder that holds `pollis.toml`), else beside the case's folder; never inside the case's folder.
2. Copy the case's folder into `games/<case folder name>/` of the toolbox.
3. Write `package.json`: `name`, `displayName`, `description`, `version` `"0.1.0"`, `publisher`, `license`, `engines` `{ "vscode": "*" }`, `categories` `["Other"]`, and `contributes.pollisToolboxes`, one entry with an `id` (letters and digits), a `title`, `"panels": []` and `"games": [{ "folder": "games/<case folder name>" }]`; and `"icon": "icon.png"`, a copy of the case's icon (`icon` in `case.toml`) at the toolbox's root. A case without an icon: ask the user to make one in the Write step of Create a Simulation Game (**Use the Title's Initials**, or **Choose an Image...** from a picture they may use), or to go on without one; never draw or download one yourself.
4. Write `README.md`: the title, what the case is, a table of the case (rounds, teams, decisions, what each game draws, how the teams are organised, news, negotiations, debrief), the files, the requirements (Pollis, and Julia on the facilitator's computer only), and the licence.
5. Package it with the plugin's script, `node <plugin>/scripts/makeToolbox.mjs pack <toolbox folder>`, where `<plugin>` is the folder two levels above this skill's. It writes `dist/<name>-0.1.0.vsix` in the toolbox's folder. Report the `.vsix`'s full path and how to install it; to change the case later, change it in `games/`, raise `version` and pack again.
