# Changelog (OctoWoW fork) – pfQuest

> Branch `octowow` = the state from Henry's "OctoWoW – HD Upgrade" install (WoW 1.12). Own changes are marked with `-- [patch]` in the code.

## Where the installed version comes from

The installed pfQuest is not a direct copy of shagu/pfQuest but a chain of forks:

1. **shagu/pfQuest**, the original. This fork is attached to it; `main` = shagu `104f356` (2025-08-25).
2. **[The-Kludge-Bureau/pfQuest](https://github.com/The-Kludge-Bureau/pfQuest)**, a maintained build (version 8.0.0).
3. **roby-brok/pfQuest** (version 8.0.1, "fork by Roby_Brok") for OctoWoW. That repository is no longer reachable on GitHub; its changes are described in the bundled README.md.
4. **Own changes** (see below).

That is why `git diff main octowow` shows a lot of changes (about 70 files). Most of them come from steps 2 and 3, not from Henry.

## Changes by Roby_Brok (according to the fork's README)

- New options **World Map Node Scale** and **Minimap Node Scale** (size of map icons).
- The quest tracker defaults to **Current Zone** instead of "All Quests".
- Fix: the map stopped following the quest log (the scan lock at login was never released).
- Fix: the **[Translate]** button never worked (`self` instead of `this`, locale tables freed too early). New option "Quest Text Translations".
- Fix: seven quests without objective markers (including the three crystal pylons in Un'Goro) via the new file `corrections.lua`.
- `/db checkdb` lists quests in your log that still lack objective data.
- pfQuest.toc: version 8.0.1, author/website changed, loads `corrections.lua`.

Compared to The-Kludge-Bureau this touches: README.md, browser.lua, compat/client.lua, config.lua, corrections.lua (new), database.lua, map.lua, quest.lua, slashcmd.lua, tracker.lua and the .toc files.

## Own changes

### database.lua / quest.lua – incremental quest giver scan
Every quest turn-in triggered a pass over the entire quest database. With the large Octo database (pfQuest-octo) this caused a visible hitch.
- `pfDatabase:BeginQuestGiverScan()` now only sets up the scan; `pfDatabase:StepQuestGiverScan()` processes it frame by frame (300 filter checks, 800 node removals, 15 quest giver placements per frame).
- quest.lua starts the scan instead of calling the blocking `SearchQuests`. The stepper runs before the 0.05 s throttle so it makes progress every frame.
- `SearchQuests` itself stays synchronous, so `/db quests` works as before.
