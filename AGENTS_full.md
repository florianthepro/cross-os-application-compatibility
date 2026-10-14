# AGENTS.md

## Verbindliche Grundlagen

- `README.md` definiert Zielbild, Architektur, Sicherheitsmodell, Nativeität, Installation, Reinstallation, Deinstallation und Repository-Struktur.
- `OS_AGENTS.md` definiert Rollen, technische Aufteilung, Contract-Modell, Validierung und Abnahmeregeln der OS-Agents.
- `COAC_ASCII_ART.txt` und `TERMINAL_UI.txt` definieren unveränderliches Branding und Terminalbedienung.
- Vor jeder Arbeit `README.md`, `OS_AGENTS.md`, `COAC_ASCII_ART.txt` und `TERMINAL_UI.txt` vollständig lesen und gemeinsam anwenden.
- Bei Widersprüchen gilt: nicht spekulieren, nichts verändern und gezielt nachfragen.

## Unveränderliche Dateien

- `README.md`, `OS_AGENTS.md`, `COAC_ASCII_ART.txt`, `TERMINAL_UI.txt` und `AGENTS.md` niemals ändern, ersetzen, umbenennen, verschieben oder löschen.
- Keine zusätzlichen Richtliniendateien außerhalb von `/scr/` anlegen.
- Ausschließlich Dateien unter `/scr/` anlegen, bearbeiten, verschieben oder löschen.

## Einstiegspunkte

- Linux: `/scr/linux/setup.sh`
- macOS: `/scr/macos/setup.sh`
- Windows 11: `/scr/windows/11/setup.ps1`
- Die Setup-Skripte sind Bootstrap, Installation, Registrierung, Reinstallation, Reparatur und Deinstallation. Die eigentliche Kompatibilitätslogik liegt in ausführbaren Komponenten unter `/scr/`.

## Arbeitsweise

- Bei eindeutigen Anforderungen autonom und vollständig innerhalb von `/scr/` arbeiten.
- Bei Unklarheiten, Widersprüchen, fehlender Betriebssystemsemantik oder sicherheitsrelevanten Annahmen gezielt nachfragen.
- Keine API-, ABI-, Rechte-, IPC-, Treiber-, Installer- oder Kernel-Semantik erfinden.
- Keine Funktion als unterstützt markieren, bevor die Abnahmeregeln aus `OS_AGENTS.md` erfüllt sind.
- Bestehende funktionierende Implementierungen erhalten; Änderungen klein, nachvollziehbar und testbar halten.
- Gemeinsame Logik zentral unter `/scr/common/` implementieren; Quell-OS-, Ziel-OS- und Paarlogik gemäß `OS_AGENTS.md` trennen.
- Keine anwendungsspezifische Sonderlösung als alleinigen Kompatibilitätspfad bauen.

## Unveränderliche technische Prinzipien

- Nativ-zu-nativ: Quellsemantik wird bis zur nativen Ziel-OS-API übersetzt und anschließend an vorhandene native Hostdienste und Hosttreiber übergeben.
- Hostbetriebssystem, Hostkernel, Grafik-Stack und Hardwaretreiber nicht ersetzen.
- Keine Rechteausweitung. Effektive Zielrechte bleiben die restriktivste Schnittmenge aus Quellrechten, Benutzerrechten, App-Policy, Nutzerfreigabe und sicherer Hostabbildung.
- Fremde Installer-, Deinstaller- und Lifecycle-Skripte niemals ungeprüft direkt auf dem Host ausführen.
- Fremde proprietäre Betriebssystemdateien, Anwendungen, Schlüssel, Logos oder Framework-Binärdateien nicht in COAC übernehmen oder verteilen.
- Normale Nutzerdokumente niemals als verwaltete Anwendungsreste behandeln oder automatisch löschen.
- Unbekannte Verträge nicht erraten. Stabilen COAC-Fehlercode ausgeben und fehlende Schnittstelle ohne sensible Daten protokollieren.

## Qualität und Sicherheit

- Für jede Implementierung Normal-, Fehler-, Rechte-, Konkurrenz-, Installations-, Deinstallations-, Bereinigungs-, Reinstallations- und Rollbackpfade testen.
- Referenzverhalten auf dem jeweiligen nativen Quellbetriebssystem differenziell prüfen.
- Keine Passwörter, Tokens, Schlüssel, Dateiinhalte oder personenbezogenen Daten in Logs, Tests oder Diagnoseexporte schreiben.
- Prüfsummen und Signaturen vor Installation oder Reinstallation validieren.
- Runtime-Wechsel nur atomar durchführen: vollständig alte oder vollständig neue Runtime, niemals gemischt.
- Bei fehlgeschlagener Vorbereitung alte Runtime unverändert aktiv lassen; bei fehlgeschlagenem Starttest vollständig zurückrollen.
- Temporäre Downloads, Staging-Daten und Bootstrap-Reste nach erfolgreichem Abschluss entfernen.

## Abschluss einer Änderung

- Geänderte Komponenten bauen und die dafür relevanten Tests ausführen.
- Repository-Grenzen prüfen: ausschließlich Änderungen unter `/scr/`.
- Neue Verträge, Module und Hostfähigkeiten gemäß `OS_AGENTS.md` registrieren und testen.
- Bekannte Abweichungen im dafür vorgesehenen Bereich unter `/scr/` maschinenlesbar dokumentieren.
- Ergebnis, ausgeführte Tests, verbleibende bekannte Abweichungen und betroffene Pfade knapp ausgeben.
