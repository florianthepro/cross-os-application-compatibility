> [!CAUTION]
> disclaimer: Made uder use of AI

# Cross-OS Application Compatibility

```text
Projektname: Cross-OS Application Compatibility
Repository:  cross-os-application-compatibility
CLI-Befehl:  cross-os-compat
Kurzform:    COAC
```

## Ziel

COAC wird einmal für den aktuellen Benutzer eingerichtet. Danach lassen sich Anwendungen und Installer fremder Betriebssysteme direkt öffnen. Die Anwendung steuert ihren normalen Installations-, Ausführungs-, Reparatur- und Deinstallationsablauf. COAC übersetzt die erwartete Quellumgebung vollständig in die nativen Schnittstellen des Zielbetriebssystems.

```text
Sicht der Anwendung
-> erwartetes Betriebssystem, APIs, ABI, Dienste, Kommunikation und Rechte wirken normal

Sicht des Zielbetriebssystems
-> Anwendung erscheint wie lokal integrierte Software
-> native Fenster, Menüs, Dateizuordnungen, Dienste und Systemfunktionen
```

Unterstütztes Zielbild:

```text
Windows: .exe, .msi
macOS:   .app, .pkg, Mach-O
Linux:   ELF, .deb, .rpm, .flatpak, .snap
```

Alle Formate sollen auf Windows, Linux und macOS integriert werden. Fehlende Implementierungen reduzieren nicht das feste Zielbild, sondern markieren offene Kompatibilitätsbereiche.

## Unveränderliches Nativ-zu-nativ-Prinzip

COAC ersetzt weder das Zielbetriebssystem noch dessen native Treiber, Grafik-Stacks, Audio-Stacks, Drucksysteme oder Geräteverwaltung.

```text
Quellaufruf
-> Quell-API-/ABI-Stub
-> semantische Übersetzung
-> Quellobjekt- und Rechteprüfung
-> kanonischer Broker-Aufruf
-> Policy Engine
-> native API des Zielbetriebssystems
-> vorhandener nativer Zielbetriebssystem-Treiber
-> Hardware oder nativer Zieldienst
-> Ergebnis, Ereignis, Handle und Fehlercode zurückübersetzen
```

Beispiele:

```text
Direct3D -> semantischer Grafikadapter -> native Host-Grafik-API -> Host-GPU-Treiber
Metal    -> semantischer Grafikadapter -> native Host-Grafik-API -> Host-GPU-Treiber
WASAPI   -> Audioadapter -> native Host-Audio-API -> Host-Audiotreiber
WinSpool -> Druckadapter -> natives Host-Drucksystem -> Host-Druckertreiber
SetupAPI -> Geräteadapter -> native Host-Geräteverwaltung -> Host-Gerätetreiber
```

COAC liefert insbesondere keine eigenen Ersatztreiber für Vulkan, Metal, Direct3D, Audio, Druck, USB, Bluetooth oder andere Hardware. Adapter übersetzen ausschließlich bis zur nativen Host-Schnittstelle. Klar begrenzte Bridge-Komponenten sind nur zulässig, wenn der Host keine passende Benutzer-Schnittstelle anbietet; auch dann bleibt der vorhandene Host-Treiber Eigentümer der Hardware.

## Vollständige Übersetzungsfläche

```text
Loader und Binärformate
-> PE/COFF, MSI, Mach-O, APP, XAR/PKG, ELF, DEB, RPM, Flatpak, Snap

Prozesse und Threads
-> Erzeugung, Beendigung, Priorität, Handles, Jobs, Prozessgruppen, Signale

Objekte und Synchronisation
-> Events, Mutexes, Semaphore, Waits, Shared Memory, Atomics

Interprozesskommunikation
-> COM/OLE, RPC, Named Pipes, Unix Sockets, D-Bus, XPC, Mach Ports

Dienste
-> Windows SCM, systemd, systemd --user, LaunchAgents, native Benutzerprozesse

Dateisystem und Speicher
-> Pfade, ACLs, Locks, Attribute, Streams, Symlinks, Groß-/Kleinschreibung, Memory Mapping

Identität und Sicherheit
-> SIDs, UID/GID, Gruppen, Access Tokens, Integritätsstufen, ACLs, Entitlements, Capabilities

Konfiguration
-> Registry, Preferences, XDG-Konfiguration, Credential Stores

Desktop
-> Fenster, Menüs, Fokus, Tray, Taskbar/Dock, Icons, Dateitypen, URL-Schemas

Datenaustausch
-> Zwischenablage, Drag-and-drop, MIME, UTType, Shell Data Objects, Datei-Handles

Netzwerk und Kryptografie
-> Sockets, DNS, Proxy, TLS, Zertifikate, Firewall-Kontext, Discovery

Medien und Geräte
-> Grafik, Compute, Audio, MIDI, Kamera, Mikrofon, Druck, Scan, HID, USB, Bluetooth

Bedienung
-> Barrierefreiheit, IME, Tastaturlayout, Touch, Stift, Locale, Zeitzone

Installation
-> Installation, Reparatur, Abhängigkeiten, Dienste, Deinstallation und Bereinigung
```

## Rechteerhaltende Übersetzung

```text
Effektive Zielberechtigung
= Quellberechtigung
∩ Berechtigung des aktuellen Zielbenutzers
∩ App-Policy
∩ ausdrückliche Nutzerfreigabe
∩ sicher verfügbare Host-Abbildung
```

```text
Deny bleibt Deny.
Fehlende Quellrechte erzeugen keine Zielrechte.
Nicht sicher abbildbare Rechte werden verweigert.
Kindprozesse erhalten höchstens die bereinigten Rechte ihres Elternprozesses.
Runtime und Broker besitzen keine pauschale Vollmacht im Namen der Anwendung.
```

Administrator-, SYSTEM-, root-, Capability- oder Entitlement-Rechte werden nie automatisch in umfassende Hostrechte übersetzt. Privilegierte Aktionen laufen einzeln über den nativen Berechtigungsdialog des Hosts. Die Freigabe gilt nur für die beschriebene Aktion und erzeugt kein dauerhaftes Admin-Token für die Anwendung.

## Kommunikation mit Hostsoftware

```text
Fremde Anwendung
-> erwartete Quell-API
-> typisierter Kommunikations-Broker
-> Rechte-, Identitäts- und Objektprüfung
-> native Host-API
-> native Zielanwendung oder Zieldienst
-> Antwort in Quellsemantik
```

```text
COM-Automation       -> Broker -> Host-COM / D-Bus / XPC
Named Pipe           -> Broker -> private Host-Pipe / Unix Socket
ShellExecute URL     -> Broker -> nativer URI-Handler
Dateidialog          -> Broker -> nativer Host-Dialog / Dokumentportal
Credential Manager   -> Broker -> Credential Manager / Keychain / libsecret
Service Control      -> Broker -> nativer Benutzer-Dienstmechanismus
```

Objektlebenszeiten, Identitäten, Handles, Timeouts, Abbruch, Rückkanäle, Impersonation und Fehlercodes werden erhalten. Fremde IPC-Namen werden pro Benutzer und Anwendung isoliert. Eine unsichere oder semantisch falsche Abbildung wird nicht durch einen mächtigeren Hostzugriff ersetzt.

## Kernelnahe Komponenten

Anwendungen mit Kernel-Treibern, Anti-Cheat oder System Extensions gehören zum Ziel höchstmöglicher Kompatibilität und werden nicht pauschal ausgeschlossen.

```text
Fremder Quell-Kernel-Treiber
-> erwarteten Treibervertrag, IOCTLs, Geräteobjekte und Sicherheitssemantik erfassen
-> kompatibles, minimales Host-Modul oder privilegiertes Host-Backend implementieren
-> Host-Signatur-, Secure-Boot- und Least-Privilege-Regeln erfüllen
-> erwartete Quellschnittstelle für die Anwendung bereitstellen
-> vorhandene native Host-Treiber weiterhin für die Hardware verwenden
```

Ein Windows-`.sys`, Linux-`.ko` oder eine macOS System Extension wird nicht unverändert in einen fremden Kernel geladen. Nativität entsteht durch eine passende Hostimplementierung des erwarteten Vertrags, nicht durch das Ersetzen des Hostkernels oder seiner Hardwaretreiber.

## Einmalige Einrichtung für den aktuellen Benutzer

```text
setup starten
-> Host und CPU-Architektur erkennen
-> vom Benutzer gewählte Format-Kompatibilitäten installieren
-> Dateizuordnungen, Menüs, Icons und URL-Schemas im Benutzerkontext registrieren
-> notwendige Benutzer-Dienste und Benutzer-Agenten einrichten
-> Verwaltung, Runtime, Broker und Anwendungsindex aktivieren
-> vollständigen Selbsttest ausführen
-> temporäre Downloads und Staging-Daten löschen
-> Bootstrap-Skript selbst entfernen
```

Die Kompatibilität gilt für den aktuellen Benutzer auf dem gesamten Host. Andere Benutzer werden nicht verändert. Nahtlose Integration umfasst Doppelklick, Öffnen mit, Startmenü/App-Launcher/Dock, Suche, Dateidialoge, Zwischenablage, Drag-and-drop, Benachrichtigungen, Druck, Audio, Medien, URL-Handler und sichere Kommunikation mit Hostsoftware.

## Automatische Laufzeit

Nach der Einrichtung gibt es keine Konvertierung und keinen vorgeschalteten Dialog pro Datei.

```text
Datei herunterladen oder öffnen
-> registrierter Format-Handler
-> Format, ABI und Architektur automatisch erkennen
-> passende Quellruntime starten
-> Quellanwendung führt ihren normalen Ablauf aus
-> Auswirkungen, Rechte und Integration verwaltet erfassen
```

Die Runtime muss nicht als Produktkategorie entscheiden, ob eine Anwendung „portabel“ oder „installiert“ ist. Sie verfolgt alle für Nativeität, Integration, Sicherheit, Wiederherstellung und Bereinigung relevanten Auswirkungen.

## ISA-Schicht

```text
Anwendungsinstruktionen
-> ISA-Ausführung nur bei erforderlicher Architekturübersetzung
-> Quell-OS-ABI-Runtime
-> semantischer Übersetzer und Broker
-> native Host-API
-> Hostkernel und native Hosttreiber
```

```text
gleiche ISA und Bitness       -> direkte CPU-Ausführung
x86-32 auf x86-64             -> CPU-Kompatibilitätsmodus plus 32-Bit-Runtime
x86/x86-64 auf ARM64          -> dynamische Binärübersetzung
ARM64 auf x86-64              -> dynamische Binärübersetzung
```

Die ISA-Schicht ist grundsätzlich ein pro Prozess arbeitender Ausführungsdienst und kein universeller Hardwaretreiber.

## Verwalteter Anwendungsbereich

```text
payload/       verwaltete Programmdateien
runtime/       benötigte Quellruntime
state/         Registry, Preferences, interne Datenbanken
cache/         Cache
audit/         Objekt- und Änderungsmanifest
mounts/        ausdrücklich freigegebene Hostpfade
logs/          redigierte Diagnoseprotokolle
```

Fremde Quellpfade existieren ausschließlich innerhalb des privaten Anwendungsnamensraums. Der Host wird nicht mit sichtbaren fremden Systemstrukturen vermischt.

Normale Nutzerdaten werden über native Dialoge, Portals oder explizite Freigaben direkt in den üblichen Hostordnern gespeichert:

```text
Dokumente, Bilder, Videos, Musik, Downloads,
Desktop, gewählte Ordner und exportierte Projekte
-> gehören dem Nutzer
-> werden nie automatisch bereinigt
```

## Installation, Deinstallation und Aufräumer

```text
Anwendungsprozess startet
-> Prozessbaum einer Application-ID zuordnen
-> verwaltete Datei-, Konfigurations-, Dienst- und IPC-Änderungen erfassen
-> Hostintegration und Berechtigungen erfassen
-> Objektmanifest fortlaufend aktualisieren
```

```text
Quell-Deinstallation endet
-> Manifest mit vorhandenem Zustand vergleichen
-> entfernte Programmkomponenten bestätigen
-> verbliebene verwaltete Programm- und Zustandsobjekte markieren
-> Nutzerdokumente und externe Freigaben strikt ausschließen
```

Der Kommandozeilen-Aufräumer arbeitet auf dem fortlaufend erstellten Objektmanifest und nicht auf bloßen Dateinamenheuristiken. Vor endgültiger Löschung zeigt er Art und Größe der internen Reste. Der Nutzer wird darauf hingewiesen, dass Einstellungen, interne Datenbanken, Anmeldestatus, Cache, private Spielstände und Wiederaufnahmeinformationen verloren gehen und bei einer Neuinstallation nicht wiederverwendet werden können.

## Update und atomare Reinstallation

Ein Update ist immer eine vollständige Reinstallation anhand der getrennt gespeicherten Benutzerkonfiguration. Es gibt keinen separaten Patchpfad.

```text
Verwaltung starten
-> lokale und veröffentlichte signierte Version vergleichen
-> bei neuer Version Hinweis im Hauptmenü anzeigen
-> keine automatische Änderung ausführen
```

```text
Reinstallation starten
-> Benutzerkonfiguration und aktivierte Module laden
-> aktuelle signierte Release-Dateien herunterladen
-> Prüfsummen und Signaturen prüfen
-> neue Runtime vollständig in Staging installieren
-> Konfiguration kontrolliert migrieren
-> Selbsttests und Validierung ausführen
-> abhängige laufende Anwendungen erkennen
-> sauberen Abschluss dieser Prozesse verlangen
-> nach vollständigem Prozessende Referenz atomar auf neue Runtime umschalten
-> Benutzerintegration reproduzierbar neu erzeugen
-> Starttest ausführen
-> erst danach alte Runtime und Staging-Reste löschen
```

Atomar bedeutet:

```text
Vor dem Umschaltpunkt -> vollständig alte Runtime
Nach dem Umschaltpunkt -> vollständig neue Runtime
Niemals               -> gemischte oder teilweise ersetzte Runtime
```

Bei fehlgeschlagener Vorbereitung bleibt die alte Runtime aktiv. Scheitert der Starttest nach dem Wechsel, werden Runtime-Referenz und Benutzerintegration vollständig auf die alte Version zurückgesetzt.

Bleibt 1:1 erhalten:

```text
aktivierte Formate, Benutzerkonfiguration, Moduloptionen,
Anwendungsindex, Anwendungen, interne Anwendungsdaten,
Berechtigungen, Ordnerfreigaben und normale Nutzerdokumente
```

## Verwaltungsmenü und Branding

Die dauerhafte ASCII-Kopfzeile liegt unter:

```text
selbes Repo -> COAC_ASCII_ART.txt
```

Die vollständigen Menüansichten und Bedienregeln liegen unter:

```text
selbes Repo -> TERMINAL_UI.txt
```

Die COAC-Grafik steht bei jeder interaktiven Ansicht über Seitentitel, Status und Menü. Bei schmalen Terminals und nicht interaktiver Ausgabe wird die kompakte COAC-Zeile verwendet.

```text
Kompatibilität
[1] Kompatibilität installieren
[2] Kompatibilität reinstallieren
[3] Kompatibilität entfernen

Anwendungen
[4] Anwendungen und verwaltete Zustände
[5] Verbliebene interne Anwendungsdaten

System
[6] Status und Diagnose
[Q] Beenden
```

## Repository-Struktur

```text
README.md
COAC_ASCII_ART.txt
TERMINAL_UI.txt
AGENTS.md
scr/
  linux/setup.sh
  macos/setup.sh
  windows/11/setup.ps1
```

Nur die drei Setup-Einstiegspunkte und weitere Dateien unter `scr/` werden implementiert. Die vier Ausgangsdateien bleiben unverändert.

## Rechtlicher Projektrahmen

```text
- Nutzer stellen ihre rechtmäßig erworbene Software selbst bereit.
- COAC implementiert unabhängige Interoperabilitäts- und Kompatibilitätsschichten.
- COAC verteilt keine fremden Anwendungen, Betriebssystemdateien, Schlüssel,
  Logos oder proprietären Framework-Binärdateien.
- Lizenzbedingungen, Signaturen, technische Schutzmaßnahmen und lokale Gesetze
  werden für die konkrete Veröffentlichung und Zielregion geprüft.
```
