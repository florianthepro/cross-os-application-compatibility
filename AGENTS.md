# AGENTS.md

## Verbindliche Grundlagen

- `README.md` definiert Zielbild, Architektur, Sicherheitsmodell, Nativeität, Installation, Reinstallation, Deinstallation und Repository-Struktur.
- `COAC_ASCII_ART.txt` und `TERMINAL_UI.txt` definieren Branding und Terminalbedienung.
- Diese Datei definiert alle Agentenrollen, Arbeitsgrenzen und die Kommunikation.
- `README.md`, `AGENTS.md`, `COAC_ASCII_ART.txt` und `TERMINAL_UI.txt` niemals ändern, ersetzen, umbenennen, verschieben oder löschen.
- `AGENTS_communication.md` ausschließlich nach dem festgelegten Append-only-Protokoll ergänzen. Bestehende Inhalte niemals ändern, überschreiben, verschieben oder löschen.

## Startbedingung

Vor jeder Arbeit müssen eindeutig bekannt sein:

- die eigene Rolle: `FULL_AGENT`, `OS_AGENT` oder `HELP_AGENT`;
- die eigene eindeutige `Agent-UUID`;
- bei `OS_AGENT` und `HELP_AGENT`: die `Agent-UUID` des zuständigen `FULL_AGENT`;
- Repository und konkreter Auftrag.

Fehlt eine dieser Informationen, Arbeit sofort abbrechen und genau die fehlende Information anfordern. Nicht raten und keine Rolle selbst annehmen.

## Agent-UUID

- Jeder Agent verwendet eine UUID im Standardformat, zum Beispiel `550e8400-e29b-41d4-a716-446655440000`.
- Vor Vergabe die vorhandenen Verzeichnisse unter `/src/` und `AGENTS_communication.md` prüfen.
- Eine bereits vorhandene UUID niemals wiederverwenden.
- Nach der Vergabe bleibt die UUID für die gesamte Aufgabe unverändert.
- Arbeitsverzeichnis von `OS_AGENT` und `HELP_AGENT`: `/src/AGENT-UUID/`.

## Rollen

### FULL_AGENT

Der `FULL_AGENT` ist der einzige Hauptagent und trägt die Gesamtverantwortung.

Aufgaben:

- `README.md`, `COAC_ASCII_ART.txt`, `TERMINAL_UI.txt` und diese Datei vollständig lesen und gemeinsam anwenden.
- Gesamtarchitektur, Prioritäten, Integration, Sicherheit und Abnahme koordinieren.
- Teilaufgaben ausschließlich über `AGENTS_communication.md` an konkrete `OS_AGENT`- oder `HELP_AGENT`-UUIDs vergeben.
- Ergebnisse der anderen Agents prüfen, testen und kontrolliert integrieren.
- Die Setup-Einstiegspunkte selbst bearbeiten:
  - `/scr/linux/setup.sh`
  - `/scr/macos/setup.sh`
  - `/scr/windows/11/setup.ps1`
- Nur der `FULL_AGENT` darf die Setup-Skripte unter `/scr/` ändern.
- Beiträge aus `/src/AGENT-UUID/` erst nach fachlicher, sicherheitstechnischer und testbarer Prüfung übernehmen.
- Keine Funktion als unterstützt markieren, bevor Loader, ABI, Rechte, Objektlebenszeit, Nebenwirkungen, Fehlerpfade und Regressionstests validiert sind.
- Runtime-Wechsel atomar durchführen: vollständig alte oder vollständig neue Runtime, niemals gemischt.
- Bei fehlgeschlagener Vorbereitung die alte Runtime aktiv lassen; bei fehlgeschlagenem Starttest vollständig zurückrollen.

Der `FULL_AGENT` darf eigene Implementierungs- und Integrationsdateien unter `/src/FULL-AGENT-UUID/` anlegen. Andere Agentenverzeichnisse darf er zur Prüfung lesen, aber nicht als Arbeitsverzeichnis verwenden oder deren Historie umschreiben.

### OS_AGENT

Der `OS_AGENT` implementiert und validiert betriebssystemspezifische COAC-Bestandteile im Auftrag des `FULL_AGENT`.

Mögliche Zuständigkeiten:

- Quell-OS-Semantik für Windows, macOS oder Linux;
- Ziel-OS-Hostadapter für Windows, macOS oder Linux;
- Validierung einer konkreten Kombination wie Windows zu Linux;
- Loader, ABI, API-Verträge, IPC, Dienste, Rechte, Installer, Syscalls, Treiberverträge und Differentialtests.

Verbindliche Laufzeitarchitektur:

```text
Fremde Anwendung oder Installer
-> Format- und Architekturdetektor
-> Quellformat-Loader
-> Import-, Symbol-, Objekt-, IPC-, Syscall- und Treibervertrags-Resolver
-> Quell-API-/ABI-Kompatibilitätsmodul
-> gemeinsames COAC-Objekt- und Zustandsmodell
-> Rechte-, Identitäts- und Kommunikations-Broker
-> zielsystemspezifischer Hostadapter
-> native API des Zielbetriebssystems
-> vorhandener nativer Hostdienst oder Hosttreiber
```

Arbeitsregeln:

- Ausschließlich im eigenen Verzeichnis `/src/AGENT-UUID/` anlegen oder bearbeiten.
- Setup-Skripte und Dateien eines anderen Agents niemals ändern.
- Quellsemantik nicht für jedes Ziel duplizieren. Gemeinsame Verträge und Objektmodelle verwenden.
- Quell-OS-Agent implementiert die Semantik, welche die fremde Anwendung erwartet.
- Ziel-OS-Agent bildet kanonische COAC-Fähigkeiten auf native Host-APIs ab.
- Paararbeit ergänzt nur Kombinationstests und echte Abweichungen zwischen Quell- und Ziel-OS.
- Keine API-, ABI-, Rechte-, IPC-, Installer-, Treiber- oder Kernelsemantik erfinden.
- Unbekannte Verträge nicht erraten. Stabilen Fehlercode und fehlende Schnittstelle an den `FULL_AGENT` melden.
- Öffentliche Spezifikationen, Header, offene Referenzimplementierungen, Binärmetadaten und rechtmäßige Referenzsysteme verwenden.
- Normal-, Fehler-, Rechte-, Konkurrenz-, Installations-, Deinstallations-, Bereinigungs- und Rollbackpfade differenziell testen.
- Hostbetriebssystem, Hostkernel, Grafik-Stack und native Hardwaretreiber nicht ersetzen.
- Quellaufrufe semantisch bis zur nativen Host-API übersetzen und anschließend vorhandene Hostdienste und Hosttreiber verwenden.
- Keine Rechteausweitung. Effektive Zielrechte sind die restriktivste Schnittmenge aus Quellrechten, Benutzerrechten, App-Policy, Nutzerfreigabe und sicherer Hostabbildung.
- Fremde Installer- oder Lifecycle-Skripte niemals ungeprüft direkt auf dem Host ausführen.
- Proprietäre Betriebssystemdateien, Schlüssel, Logos oder nicht freigegebene Framework-Binärdateien nicht übernehmen oder verteilen.
- Ergebnisse ausschließlich über das Kommunikationsprotokoll an den zuständigen `FULL_AGENT` melden.

### HELP_AGENT

Der `HELP_AGENT` unterstützt ausschließlich den zugeordneten `FULL_AGENT` bei einer klar abgegrenzten Teilaufgabe.

Arbeitsregeln:

- Nur den ausdrücklich über `AGENTS_communication.md` erteilten Teilauftrag bearbeiten.
- Ausschließlich im eigenen Verzeichnis `/src/AGENT-UUID/` anlegen oder bearbeiten.
- Setup-Skripte und Dateien eines anderen Agents niemals ändern.
- Keine Architektur-, API-, ABI-, Sicherheits- oder Betriebssystemsemantik erfinden.
- Keine Aufgabe eigenmächtig erweitern oder an andere Agents weitergeben.
- Bestehende Arbeit anderer Agents nicht überschreiben.
- Bei Konflikten, Widersprüchen, fehlenden Voraussetzungen oder notwendiger Auftragsänderung an den `FULL_AGENT` berichten und Arbeit am betroffenen Punkt stoppen.
- Relevante Tests für den eigenen Teilauftrag ausführen.
- Den Gesamtauftrag niemals selbst als abgeschlossen erklären.
- Ergebnisse ausschließlich über das Kommunikationsprotokoll an den zuständigen `FULL_AGENT` melden.

## Arbeitsbereiche

```text
/scr/linux/setup.sh
/scr/macos/setup.sh
/scr/windows/11/setup.ps1
```

- Nur `FULL_AGENT` bearbeitet diese Setup-Skripte.

```text
/src/AGENT-UUID/
```

- `OS_AGENT` und `HELP_AGENT` arbeiten ausschließlich im eigenen UUID-Verzeichnis.
- Jeder Agent darf nur sein eigenes UUID-Verzeichnis beschreiben.
- Andere UUID-Verzeichnisse sind nur lesbar, wenn der `FULL_AGENT` dies zur Aufgabe ausdrücklich verlangt.
- Der `FULL_AGENT` integriert geprüfte Ergebnisse, ohne die ursprünglichen Agentenbeiträge oder deren Historie zu löschen.

## Kommunikation

Alle Kommunikation erfolgt ausschließlich über `AGENTS_communication.md`.

- `OS_AGENT` und `HELP_AGENT` kommunizieren nur mit dem zuständigen `FULL_AGENT`, niemals direkt miteinander.
- Der `FULL_AGENT` adressiert einen Agenten ausschließlich mit `To-Agent-UUID`.
- Jeder `OS_AGENT` und `HELP_AGENT` schreibt ausschließlich unter seiner eigenen `Agent-UUID`.
- Ein Agent darf keinen Block eines anderen Agents erzeugen, ändern oder löschen.
- Bestehende Blöcke und Nachrichten bleiben dauerhaft erhalten.
- Jede neue Nachricht wird als neuer Block am Dateiende angefügt.

Nachricht eines `OS_AGENT` oder `HELP_AGENT`:

```text
Agent-UUID:
```

```text
<Nachricht an FULL_AGENT>
```

Nachricht des `FULL_AGENT` an einen Agenten:

```text
To-Agent-UUID:
```

```text
<Auftrag oder Antwort>
```

`Agent-UUID` und `To-Agent-UUID` sind durch die tatsächliche UUID zu ersetzen, zum Beispiel:

```text
To-550e8400-e29b-41d4-a716-446655440000:
```

```text
Implementiere den angegebenen Loader-Vertrag und melde Tests und Abweichungen zurück.
```

## Append-only- und Push-Protokoll

Vor jeder Nachricht:

1. Aktuellen Repository-Stand abrufen.
2. `AGENTS_communication.md` vollständig neu lesen.
3. Prüfen, ob seit dem letzten Stand neue Nachrichten an die eigene UUID vorliegen.
4. Prüfen, ob die eigene geplante Nachricht noch aktuell und nicht bereits vorhanden ist.
5. Exakt einen neuen Block am Dateiende anhängen.
6. Speichern, committen und pushen.
7. Nach dem Push den Remote-Stand erneut abrufen.
8. Prüfen, ob der eigene Block exakt und vollständig im Remote-Stand vorhanden ist.

Bei Push-Reject oder gleichzeitigem Schreibzugriff:

1. Nicht force-pushen.
2. Aktuellen Remote-Stand abrufen.
3. `AGENTS_communication.md` erneut vollständig lesen.
4. Prüfen, ob der eigene Block bereits durchkam.
5. Falls vorhanden, keinen zweiten identischen Block hinzufügen.
6. Falls nicht vorhanden, denselben Block unverändert erneut am Dateiende anhängen.
7. Erneut committen und pushen.
8. Remote-Prüfung wiederholen, bis eindeutig feststeht, ob der Block angenommen wurde.

Verboten:

- bestehende Blöcke bearbeiten, sortieren, zusammenführen oder löschen;
- die Datei neu erzeugen oder ihren bisherigen Inhalt ersetzen;
- Rebase-, Merge- oder Konfliktauflösung, die bestehende Kommunikation entfernt;
- Force-Push;
- Kommunikation außerhalb von `AGENTS_communication.md` als verbindlich behandeln;
- mehrere Nachrichten in einem Block vermischen.

## Inhalt einer Rückmeldung

Ein Ergebnisblock an den `FULL_AGENT` enthält knapp und vollständig:

```text
Rolle und Auftrag
Status
Ergebnis
angelegte oder geänderte Pfade unter /src/AGENT-UUID/
ausgeführte Tests und Ergebnisse
bekannte Abweichungen oder Risiken
offene Fragen oder benötigte Folgearbeit
```

Ein Agent meldet keine Unterstützung, wenn relevante Tests fehlen oder fehlschlagen.

## Allgemeine Qualitäts- und Sicherheitsregeln

- Keine Passwörter, Tokens, privaten Schlüssel, Dateiinhalte oder personenbezogenen Daten in Kommunikation, Logs, Tests oder Diagnoseexporte schreiben.
- Normale Dokumente, Bilder, Videos, Musik, Downloads und exportierte Projekte niemals als verwaltete Anwendungsreste behandeln oder automatisch löschen.
- Prüfsummen und Signaturen vor Installation oder Reinstallation validieren.
- Temporäre Downloads, Staging-Daten und Bootstrap-Reste nach erfolgreichem Abschluss entfernen.
- Änderungen klein, nachvollziehbar, reproduzierbar und testbar halten.
- Bekannte Abweichungen maschinenlesbar im eigenen Arbeitsverzeichnis dokumentieren.
- Bei Unklarheiten oder Widersprüchen nicht spekulieren. `OS_AGENT` und `HELP_AGENT` fragen den `FULL_AGENT`; der `FULL_AGENT` fordert die fehlende Entscheidung beim Auftraggeber an.
