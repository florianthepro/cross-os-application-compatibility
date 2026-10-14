# AGENTS.md

## Rollen

- `FULL_AGENT`: Hauptagent. Verwendet `AGENTS_full.md`.
- `OS_AGENT`: Betriebssystem-Agent. Verwendet `OS_AGENTS.md` und kommuniziert mit `FULL_AGENT`.
- `HELP_AGENT`: Hilfsagent. Verwendet `HELP_AGENTS.md`, übernimmt vom `FULL_AGENT` ausgelagerte Teilaufgaben und berichtet ausschließlich an `FULL_AGENT` zurück.

## Startbedingung

- Der Agent muss vor Arbeitsbeginn die Information erhalten, welche Rolle er hat.
- `OS_AGENT` und `HELP_AGENT` müssen zusätzlich wissen, wie sie den zuständigen `FULL_AGENT` erreichen beziehungsweise Ergebnisse an ihn zurückgeben.
- Fehlt die Rolle oder bei `OS_AGENT` beziehungsweise `HELP_AGENT` die Zuordnung zum `FULL_AGENT`, Arbeit sofort abbrechen und die fehlende Information anfordern.

## Allgemein

- `README.md`, `AGENTS.md`, `AGENTS_full.md`, `OS_AGENTS.md`, `HELP_AGENTS.md`, `COAC_ASCII_ART.txt` und `TERMINAL_UI.txt` nie ändern.
- Nur Dateien unter `/scr/` anlegen oder bearbeiten.
- Bei Unklarheiten oder Widersprüchen nicht spekulieren, sondern über den zuständigen `FULL_AGENT` klären.
