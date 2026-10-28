---
name: package-toolbox
description: "Package a toolbox: build a Pollis toolbox (a .vsix extension) from Pollis menu entries the user picks, in the menus and order they want, for example one toolbox per course for a teacher's students. Triggers include 'make a toolbox from these panels', 'package these menu entries as an extension', 'build a toolbox for my course', 'Extension Agent', 'build an extension from this spec', 'package my new panel'. The create-panel skill hands its new panels over to this one."
---

# Package a Toolbox

Builds a Pollis toolbox from a spec: a list of Pollis menu entries (the panels) and the menus and order they go in. The result is a `.vsix` file the user installs in Pollis with **Extensions: Install from VSIX...**, or gives to others (a teacher to their students, say). A toolbox has no code: its panels are copies of Pollis panels with their wikis and notebook tutorials.

The mechanical work is done by the builder script, `makeToolbox.mjs`, in the `scripts` folder of this plugin: two folders up from this SKILL.md, then `scripts/` (this file is `<plugin>/skills/package-toolbox/SKILL.md`, the script is `<plugin>/scripts/makeToolbox.mjs`). If there is no `scripts` folder there (the skills were installed on their own, in `~/.agents/skills`), it is `~/.pollis/agents/pollis-agents/scripts`. Use its absolute path in the commands below, quoted. You do the parts that need judgment.

It needs Node.js 22 or later (`node --version`); if Node is missing or older, tell the user to install it from nodejs.org and stop. It reads Pollis' panels from the public Pollis repository on GitHub, so it needs the internet; nothing is installed and Pollis itself is not changed.

## The builder

```
node "<plugin>/scripts/makeToolbox.mjs" list [words...]
node "<plugin>/scripts/makeToolbox.mjs" build <spec.toml> --out <folder> [--overwrite]
node "<plugin>/scripts/makeToolbox.mjs" pack <folder>/<name>
```

- `list` prints the panels whose id, title, menu title or command contain all the words, with their commands and menu titles; a panel marked NOT PORTABLE cannot go in a toolbox (it has its own code), with the reason. `list` alone also prints the menus a toolbox can go in.
- `build` writes `<folder>/<name>/` (package.json, panels/, wiki/, notebooks/, docs/ when it has documentation, icon.png, README.md) and `<folder>/<name>-<version>.vsix`.
- `pack` packages a toolbox folder again, after its README was edited; the `.vsix` is written next to the folder.

## The spec

```toml
[extension]
name = "pollis-toolbox-stats101"     # lower case letters, digits and hyphens
displayName = "Statistics 101"
description = "The panels of the Statistics 101 course."
version = "0.1.0"                    # optional, default 0.1.0
author = "Ana Silva"                 # optional: shown in Pollis as the publisher

[[menu]]                             # one or more
id = "stats101"
title = "Statistics 101"             # the submenu's title, at the end of Extend
order = 1                            # optional: the order among the toolboxes there

[[menu.items]]                       # the entries, in the order wanted
command = "chiara.statistics.lm"     # the menu entry's command (from list), or panel = "lm", or toml = "garch.toml"
title = "1. Linear Regression"       # optional: the menu title, default the one in Pollis

[[menu.documents]]                   # optional, one or more: the toolbox's documentation
title = "Statistics 101 Course Notes"
description = "The course's notes, with the exercises of each week."   # optional
source = "notes/stats101.tex"        # the main .tex file, relative to the spec
```

- **Origin**: each `[[menu.items]]` names a menu entry by its `command`, or the panel by `panel = "<id>"`. The user names entries by their menu titles: find them with `list` and say which command you chose when there is more than one match. A command that opens a panel on a given model (shown by `list`) opens the copy on that model too.
- **A panel of the user's own** (written by the `create-panel` skill, or by hand) is `toml = "<path>"`, relative to the spec: its id is the file name, and its `wiki/` and `notebooks/` folders next to it are copied with it. The build checks it with the validator first and stops on an error: fix it with the `create-panel` skill's reference, then build again.
- **Destination**: a toolbox does not choose its menu. Its submenu goes at the end of Extend, and the user can move it, or any of its panels, to another menu with the toolbox's Edit button. `inline = true` puts the entries straight into Extend, without a submenu. Items take `group` and `order` (default: their position).
- **Documentation**: a toolbox can bring its own documents, which Pollis lists in **Help > Documentation > Toolboxes** and compiles with PollisTeX (the XeTeX of Tectonic) the first time one is opened. Each `[[menu.documents]]` names the main `.tex` file (letters, digits, `-` and `_` in its name); the build copies the `.tex` and `.bib` files of its folder and its `resources/` folder (images, `.png` or `.jpg`) into `docs/<main file's name>/`. The sources must compile with XeTeX: `fontspec` rather than `inputenc`, no shell escape. The user writes the documents, or asks you to; do not invent their content.
- Optional `prefix` in `[extension]` (default: the name without `pollis-toolbox-`) names the copies: panel `<prefix>.<id>`, wiki and notebook folders `<prefix>.<folder>`.

## Where the toolbox is saved

In the toolboxes folder, `~/.pollis/toolboxes/` (create it when missing), next to the rest of what Pollis keeps: the spec, the toolbox folder and the `.vsix` go there. When the open folder is a Pollis Workspace (it has a `pollis.toml` file), save them in it instead, since that is where the user's work belongs. Use another folder only when the user names one; never a folder of a particular computer.

## Steps

1. **Understand the request.** Find each panel the user names with `list`; if a name matches several panels or none, ask. If something is missing (name, display name, description, the author to show as publisher, where the entries go, their order, whether the toolbox comes with documentation and, if so, where its main `.tex` file is), ask once, offering sensible defaults. In the same message, ask where to publish it (step 6): nowhere (the `.vsix` only, the default); in a GitHub repository of the user's own, and if so the account or organisation, the repository name (default: the toolbox name) and public or private (default: public); or proposed for the Toolboxes section of Pollis, as a pull request to the Pollis toolboxes repository. Do not invent a description: ask, or propose one for approval.
2. **Write the spec** as `<name>.toml` in the toolboxes folder (see *Where the toolbox is saved*), and show it.
3. **Run `build`** with `--out` that folder (`--overwrite` only when the user agreed to replace an existing toolbox folder of that name). It stops with a clear message on what cannot be packaged: a NOT PORTABLE panel, a panel of the user's own with errors, a gallery or dialog (not a panel), an unknown command, panel or menu, a panel listed twice. Report it, and continue without the entry only if the user agrees.
4. **Finish the README** (`<folder>/<name>/README.md`): fill the "What it covers" column, one short line per panel, from the panel's TOML in `<folder>/<name>/panels/` (its models and key points), for example "Ordinary, weighted and robust least squares, with diagnostics". Keep the rest. Show it to the user, then run `pack` on the toolbox folder.
5. **Report**: the panels and where they go, anything left out and why, and the full path of the `.vsix`; offer to show it in the file manager (`open -R <file>` on macOS, `explorer /select,<file>` on Windows, `xdg-open <folder>` on Linux). Tell the user how to install it: in Pollis, run **Extensions: Install from VSIX...** from the Command Palette and pick the file (students do the same with the file their teacher gives them). The panels appear in the menus at once. To change the toolbox later, edit the spec, raise `version`, build again and install the new `.vsix` over the old one; to remove it, uninstall it in the Extensions pane.
6. **Publish**, only as the user chose in step 1 (if they chose nowhere, offer the two other ways once, at the end of the report). Both need the GitHub CLI, signed in: check with `gh auth status`; if it is missing or signed out, tell the user to install it from cli.github.com and run `gh auth login`, and stop here. Never push without the user's yes, and never force-push.

   **In the user's own repository.** In the toolbox folder:
   - Write a `.gitignore` with `*.vsix` and `.DS_Store`; the `.vsix` is not part of the sources.
   - `git init -b main`, `git add -A`, `git commit -m "<displayName> <version>"`.
   - `gh repo create <owner>/<repository> --public --source . --push` (or `--private`). If the repository exists already, ask before pushing to it.
   - `gh release create v<version> "<the .vsix>" --title "<displayName> <version>" --notes "<the description>"`: the release is where others download the `.vsix`.

   Give the user the repository and release links. For a new version later: raise `version`, build and pack again, commit, push, and make a new release. A repository of one's own does not list the toolbox in the Toolboxes section of Pollis' Extensions pane; the next way does.

   **Proposed for the Toolboxes section of Pollis**, as a pull request to https://github.com/Trumpingtons/pollis-toolboxes (read its CONTRIBUTING.md first: one toolbox folder per pull request, version 0.1.0 for a new toolbox, the checks it runs). In the toolboxes folder:
   - `gh repo view Trumpingtons/pollis-toolboxes --json viewerPermission`: if it is `ADMIN`, `MAINTAIN` or `WRITE`, the user maintains it: clone it (`gh repo clone Trumpingtons/pollis-toolboxes`) instead of forking, and the pull request below becomes a commit to main, which publishes the toolbox at once; say so and ask before pushing.
   - Otherwise `gh repo fork Trumpingtons/pollis-toolboxes --clone`, and in the clone `git pull upstream main` to start from its latest state.
   - Copy the toolbox folder into the clone, without the `.vsix` and the spec: `package.json`, `README.md`, the icon, `panels/`, `wiki/`, `notebooks/` and `docs/`. Add the toolbox's row to the table in the clone's `README.md`, like the other rows.
   - Run the check the pull request will run, and fix every error before going on: `node scripts/checkToolboxes.ts --validator <this plugin>/scripts/validatePanel.mjs --base upstream/main` (`--base origin/main` in a maintainer's clone).
   - Show the user what will be sent, then commit (`<displayName> <version>`), `git push origin main`, and `gh pr create --repo Trumpingtons/pollis-toolboxes --head <the user's account>:main --title "<displayName> <version>" --body "<filled from .github/pull_request_template.md>"`.

   Give the user the pull request link: the check runs on it at once and a maintainer reviews it; when it is merged, Pollis lists the toolbox within a few minutes. For a change later: raise `version`, copy the folder again into the clone, check, commit, push; an open pull request takes the new commits.

## Rules

- Every panel of the toolbox is an independent copy: its own editor tab, its own wiki and notebook folders, its own saved examples and references. Pollis keeps its own panels unchanged. Say so if the user asks whether the toolbox replaces anything.
- A copy is made from the current Pollis panels; when Pollis improves a panel later, the toolbox keeps the version it was built with until it is built again.
- If a download fails, say so with the message the builder printed. Behind a proxy, recent Node versions use `HTTPS_PROXY` when `NODE_USE_ENV_PROXY=1` is set too.
- Never use DataFrames, never name a university, say Open VSX, never "VS Code Marketplace" (in the README and in what you tell the user).
