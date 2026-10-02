# Changelog (OctoWoW-Fork) – pfQuest

> Branch `octowow` = Stand aus Henrys Installation „OctoWoW – HD Upgrade“ (WoW 1.12). Eigene Anpassungen sind im Code mit `-- [patch]` markiert.

## Herkunft des installierten Stands

Der installierte pfQuest ist keine direkte Kopie von shagu/pfQuest, sondern eine Kette von Forks:

1. **shagu/pfQuest**, das Original. Dieser Fork hängt daran; `main` = shagu `104f356` (2025-08-25).
2. **[The-Kludge-Bureau/pfQuest](https://github.com/The-Kludge-Bureau/pfQuest)**, ein weitergepflegter Build (Version 8.0.0).
3. **roby-brok/pfQuest** (Version 8.0.1, „fork by Roby_Brok“) für OctoWoW. Das Repo ist auf GitHub nicht mehr erreichbar, seine Änderungen sind in der mitgelieferten README.md beschrieben.
4. **Eigene Anpassungen** (siehe unten).

Deshalb zeigt `git diff main octowow` sehr viele Änderungen (ca. 70 Dateien). Der größte Teil stammt aus Schritt 2 und 3, nicht von Henry.

## Änderungen von Roby_Brok (laut README des Forks)

- Neue Optionen **World Map Node Scale** und **Minimap Node Scale** (Größe der Kartensymbole).
- Quest-Tracker steht standardmäßig auf **Current Zone** statt „All Quests“.
- Fix: Die Karte hörte auf, dem Questlog zu folgen (Scan-Sperre beim Login wurde nie freigegeben).
- Fix: Der **[Translate]**-Knopf funktionierte nie (`self` statt `this`, Locale-Tabellen zu früh freigegeben). Neue Option „Quest Text Translations“.
- Fix: Sieben Quests ohne Zielmarkierungen (u. a. die drei Kristallpylonen in Un'Goro) über die neue Datei `corrections.lua`.
- `/db checkdb` listet Quests im Log, denen noch Zieldaten fehlen.
- pfQuest.toc: Version 8.0.1, Autor/Website angepasst, lädt `corrections.lua`.

Gegenüber The-Kludge-Bureau betrifft das: README.md, browser.lua, compat/client.lua, config.lua, corrections.lua (neu), database.lua, map.lua, quest.lua, slashcmd.lua, tracker.lua und die .toc-Dateien.

## Eigene Änderungen

### database.lua / quest.lua – inkrementeller Questgeber-Scan
Jeder Quest-Abschluss löste einen Durchlauf über die komplette Questdatenbank aus. Mit der großen Octo-Datenbank (pfQuest-octo) war das ein sichtbarer Hänger.
- `pfDatabase:BeginQuestGiverScan()` legt nur noch den Scan an, `pfDatabase:StepQuestGiverScan()` arbeitet ihn Frame für Frame ab (300 Filterprüfungen, 800 Node-Löschungen, 15 Questgeber-Platzierungen pro Frame).
- In quest.lua wird statt des blockierenden `SearchQuests` nur der Scan gestartet. Der Stepper läuft vor der 0,05-s-Drossel, damit er jeden Frame vorankommt.
- `SearchQuests` selbst bleibt synchron, `/db quests` funktioniert unverändert.
