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
- die eigene eindeutige Agent-UUID;
- bei `OS_AGENT` und `HELP_AGENT`: die Agent-UUID des zuständigen `FULL_AGENT`;
- Repository und konkreter Auftrag.

Fehlt eine dieser Informationen, Arbeit sofort abbrechen und genau die fehlende Information anfordern. Nicht raten und keine Rolle selbst annehmen.

## Agent-UUID

- Jeder Agent verwendet eine UUID im Standardformat, zum Beispiel `550e8400-e29b-41d4-a716-446655440000`.
- Vor Vergabe die vorhandenen Verzeichnisse unter `/src/` und `AGENTS_communication.md` prüfen.
- Eine bereits vorhandene UUID niemals wiederverwenden.
- Nach der Vergabe bleibt die UUID als Name und Identifier für die gesamte Aufgabe unverändert.
- Arbeitsverzeichnis von `OS_AGENT` und `HELP_AGENT`: `/src/AGENT-UUID/`.

## Rollen

### FULL_AGENT

Der `FULL_AGENT` ist der einzige Hauptagent und trägt die Gesamtverantwortung.

Aufgaben:

- `README.md`, `COAC_ASCII_ART.txt`, `TERMINAL_UI.txt` und diese Datei vollständig lesen und gemeinsam anwenden.
- Gesamtarchitektur, Prioritäten, Integration, Sicherheit und Abnahme koordinieren.
- Teilaufgaben ausschließlich über `AGENTS_communication.md` an konkrete `OS_AGENT`- oder `HELP_AGENT`-UUIDs vergeben.
- Ergebnisse der anderen Agents prüfen, testen und kontrolliert integrieren.
- Als einziger Agent alle Dateien und Verzeichnisse unter `/scr/` anlegen, bearbeiten, verschieben und löschen.
- Die folgenden drei Setup-Einstiegspunkte müssen immer vorhanden und funktionsfähig sein:
  - `/scr/linux/setup.sh`
  - `/scr/macos/setup.sh`
  - `/scr/windows/11/setup.ps1`
- Weitere für Runtime, Loader, Broker, Hostadapter, Verwaltung, Installation, Reinstallation, Reparatur, Deinstallation, Tests und Build benötigte Dateien unter `/scr/` anlegen und pflegen.
- Beiträge aus `/src/AGENT-UUID/` erst nach fachlicher, sicherheitstechnischer und testbarer Prüfung nach `/scr/` integrieren.
- Keine Funktion als unterstützt markieren, bevor Loader, ABI, Rechte, Objektlebenszeit, Nebenwirkungen, Fehlerpfade und Regressionstests validiert sind.
- Runtime-Wechsel atomar durchführen: vollständig alte oder vollständig neue Runtime, niemals gemischt.
- Bei fehlgeschlagener Vorbereitung die alte Runtime aktiv lassen; bei fehlgeschlagenem Starttest vollständig zurückrollen.

Der `FULL_AGENT` darf eigene Implementierungs- und Integrationsdateien unter `/src/FULL-AGENT-UUID/` anlegen. Andere Agentenverzeichnisse darf er zur Prüfung lesen, aber nicht als Arbeitsverzeichnis verwenden oder deren Historie umschreiben.

#### Repository-Bezug der Setup-Skripte

Die Setup-Skripte müssen sowohl aus einem geklonten Repository als auch als einzeln heruntergeladene Datei funktionieren. Alle weiteren benötigten Dateien werden aus dem konfigurierten Repository bezogen.

Der `FULL_AGENT` stellt in jedem der drei Setup-Einstiegspunkte direkt nach Shebang beziehungsweise Skript-Metadaten mindestens diese zentralen Variablen bereit:

Linux und macOS:

```sh
REPO_OWNER="${REPO_OWNER:-<REPO_OWNER>}"
REPO_NAME="${REPO_NAME:-cross-os-application-compatibility}"
REPO_REF="${REPO_REF:-main}"
```

Windows PowerShell:

```powershell
$REPO_OWNER = if ($env:REPO_OWNER) { $env:REPO_OWNER } else { '<REPO_OWNER>' }
$REPO_NAME = if ($env:REPO_NAME) { $env:REPO_NAME } else { 'cross-os-application-compatibility' }
$REPO_REF = if ($env:REPO_REF) { $env:REPO_REF } else { 'main' }
```

Regeln:

- `<REPO_OWNER>` wird vor einer Veröffentlichung durch den tatsächlichen Repository-Owner ersetzt.
- Repository-Owner, Repository-Name, Branch, Tag oder Commit niemals an weiteren Stellen dupliziert fest eintragen.
- Alle Remote-Adressen ausschließlich aus `REPO_OWNER`, `REPO_NAME` und `REPO_REF` ableiten.
- Ein explizit gesetzter Umgebungswert darf die Standardkonfiguration überschreiben.
- Bei geklontem Repository zuerst den lokalen Repository-Root und passende lokale Dateien verwenden.
- Fehlen benötigte lokale Dateien oder wurde nur das Setup-Skript heruntergeladen, die erforderlichen Dateien aus dem konfigurierten Remote-Repository laden.
- Vor der Nutzung geladener Inhalte Signatur beziehungsweise veröffentlichte Prüfsumme validieren.
- Keine beliebigen Dateien aus einem veränderlichen Branch ungeprüft ausführen.
- Für eine Installation bevorzugt einen signierten Release, Tag oder unveränderlichen Commit verwenden. `REPO_REF=main` dient nur als konfigurierbarer Entwicklungsstandard.
- Downloads zunächst in ein Staging-Verzeichnis schreiben, vollständig validieren und erst danach atomar installieren.
- Teilweise Downloads, Validierungsfehler oder nicht erreichbare Repository-Inhalte dürfen keine bestehende Installation verändern.
- Temporäre Downloads und Staging-Reste nach Erfolg oder kontrolliertem Abbruch entfernen.
- Reinstallation und Updateprüfung verwenden dieselbe Repository-Konfiguration und die gespeicherte Benutzerkonfiguration.
- Das Setup-Skript bleibt Bootstrap. Die eigentliche Kompatibilitätslogik wird als Repository-Inhalt unter `/scr/` gepflegt und bedarfsgerecht installiert.

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
- Dateien unter `/scr/`, Setup-Skripte und Dateien eines anderen Agents niemals ändern.
- Für eine spätere Integration vollständige Quelldateien, Tests, Buildinformationen und Integrationshinweise im eigenen UUID-Verzeichnis bereitstellen.
- Repository-, Download- oder Installationslogik nicht eigenmächtig in Setup-Skripte integrieren; notwendige Änderungen dem `FULL_AGENT` über `AGENTS_communication.md` melden.
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
- Dateien unter `/scr/`, Setup-Skripte und Dateien eines anderen Agents niemals ändern.
- Für eine spätere Integration vollständige Ergebnisse, Tests und Integrationshinweise im eigenen UUID-Verzeichnis bereitstellen.
- Erforderliche Änderungen unter `/scr/` ausschließlich dem `FULL_AGENT` melden.
- Keine Architektur-, API-, ABI-, Sicherheits- oder Betriebssystemsemantik erfinden.
- Keine Aufgabe eigenmächtig erweitern oder an andere Agents weitergeben.
- Bestehende Arbeit anderer Agents nicht überschreiben.
- Bei Konflikten, Widersprüchen, fehlenden Voraussetzungen oder notwendiger Auftragsänderung an den `FULL_AGENT` berichten und Arbeit am betroffenen Punkt stoppen.
- Relevante Tests für den eigenen Teilauftrag ausführen.
- Den Gesamtauftrag niemals selbst als abgeschlossen erklären.
- Ergebnisse ausschließlich über das Kommunikationsprotokoll an den zuständigen `FULL_AGENT` melden.

## Arbeitsbereiche

```text
/scr/
```

- Nur der `FULL_AGENT` darf Dateien und Verzeichnisse unter `/scr/` anlegen, bearbeiten, verschieben oder löschen.
- Die drei verpflichtenden Setup-Einstiegspunkte müssen erhalten bleiben:
  - `/scr/linux/setup.sh`
  - `/scr/macos/setup.sh`
  - `/scr/windows/11/setup.ps1`
- Weitere ausführbare Komponenten, Quellen, Builds, Tests und Ressourcen dürfen unter `/scr/` liegen.

```text
/src/AGENT-UUID/
```

- `OS_AGENT` und `HELP_AGENT` arbeiten ausschließlich im eigenen UUID-Verzeichnis.
- Jeder Agent darf nur sein eigenes UUID-Verzeichnis beschreiben.
- Andere UUID-Verzeichnisse sind nur lesbar, wenn der `FULL_AGENT` dies zur Aufgabe ausdrücklich verlangt.
- Der `FULL_AGENT` integriert geprüfte Ergebnisse nach `/scr/`, ohne die ursprünglichen Agentenbeiträge oder deren Historie zu löschen.

## Kommunikation

Alle Kommunikation erfolgt ausschließlich über `AGENTS_communication.md`.

- `OS_AGENT` und `HELP_AGENT` kommunizieren nur mit dem zuständigen `FULL_AGENT`, niemals direkt miteinander.
- Der `FULL_AGENT` adressiert einen Agenten ausschließlich mit `To-Agent-UUID`.
- Jeder `OS_AGENT` und `HELP_AGENT` schreibt ausschließlich unter seiner eigenen Agent-UUID.
- Ein Agent darf keinen Block eines anderen Agents erzeugen, ändern oder löschen.
- Bestehende Blöcke und Nachrichten bleiben dauerhaft erhalten.
- Jede neue Nachricht wird als neuer Block am Dateiende angefügt.

Nachricht eines `OS_AGENT` oder `HELP_AGENT`:

```text
Agent-UUID:
<Nachricht an FULL_AGENT>
```

Nachricht des `FULL_AGENT` an einen Agenten:

```text
To-Agent-UUID:
<Auftrag oder Antwort>
```

Agent-UUID und To-Agent-UUID sind durch die tatsächliche UUID zu ersetzen, zum Beispiel:

```text
To-550e8400-e29b-41d4-a716-446655440000:
Implementiere den angegebenen Loader-Vertrag und melde Tests und Abweichungen zurück.
```

## Append-only- und Push-Protokoll

Vor jeder Nachricht:

- Aktuellen Repository-Stand abrufen.
- `AGENTS_communication.md` vollständig neu lesen.
- Prüfen, ob seit dem letzten Stand neue Nachrichten an die eigene UUID vorliegen.
- Prüfen, ob die eigene geplante Nachricht noch aktuell und nicht bereits vorhanden ist.
- Exakt einen neuen Block am Dateiende anhängen.
- Speichern, committen und pushen.
- Nach dem Push den Remote-Stand erneut abrufen.
- Prüfen, ob der eigene Block exakt und vollständig im Remote-Stand vorhanden ist.

Bei Push-Reject oder gleichzeitigem Schreibzugriff:

- Nicht force-pushen.
- Aktuellen Remote-Stand abrufen.
- `AGENTS_communication.md` erneut vollständig lesen.
- Prüfen, ob der eigene Block bereits durchkam.
- Falls vorhanden, keinen zweiten identischen Block hinzufügen.
- Falls nicht vorhanden, denselben Block unverändert erneut am Dateiende anhängen.
- Erneut committen und pushen.
- Remote-Prüfung wiederholen, bis eindeutig feststeht, ob der Block angenommen wurde.

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
Integrationshinweise für /scr/
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
