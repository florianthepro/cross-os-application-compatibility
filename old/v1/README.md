# Cross-OS Application Compatibility

# Projektname und Repository

```text
Projektname: Cross-OS Application Compatibility
Repository:  cross-os-application-compatibility
CLI-Befehl:  cross-os-compat
Kurzform in Statuszeilen: COAC
```

Der Name beschreibt ohne Fantasiebegriff direkt den Zweck: Anwendungskompatibilität zwischen Windows, Linux und macOS.

Die feste Kommandozeilengrafik, Menüansichten, Statussymbole, Texte und Darstellungsregeln liegen unter:

```text
selbes Repo -> TERMINAL_UI.txt
```

Die Implementierung übernimmt diese Darstellung unverändert. Bei Terminals ohne Unicode-Unterstützung verwendet sie automatisch den ebenfalls dort definierten ASCII-Fallback.

## Bedienmodell

```text
cross-os-compat
-> öffnet das Hauptmenü

cross-os-compat status
-> zeigt denselben Status ohne interaktive Eingabe

cross-os-compat reinstall
-> startet die vollständige Reinstallation anhand der vorhandenen Benutzerkonfiguration

cross-os-compat uninstall
-> öffnet die sichere Deinstallation
```

Das Hauptmenü ist aufgabenorientiert. Es zeigt zuerst Systemzustand, Updatehinweis und verfügbare Aktionen. Technische Details bleiben unter „Status und Diagnose“, damit die Standardansicht übersichtlich bleibt.

## Feste Menüführung

```text
Start
-> Status und Update prüfen
-> Hauptmenü anzeigen

Kompatibilität einrichten
-> Zielbetriebssystem auswählen
-> Formate gemeinsam anzeigen
-> benötigte Komponenten vor Installation zusammenfassen
-> installieren
-> Dateizuordnungen testen

Anwendungen
-> nach Quellbetriebssystem gruppieren
-> installiert, portabel oder Datenrest anzeigen
-> Anwendung öffnen, reparieren, entfernen oder interne Daten bereinigen

Reinstallieren
-> erhaltene Konfiguration zusammenfassen
-> aktuelle Version prüfen
-> signierte Komponenten laden
-> atomar ersetzen

Kompatibilität entfernen
-> betroffene Anwendungen und interne Daten vorab anzeigen
-> Nutzerdaten ausdrücklich als nicht betroffen kennzeichnen
-> eindeutige Löschbestätigung verlangen
```

## Interaktionsregeln

```text
- Ziffern für Hauptaktionen
- B für Zurück
- Q für Beenden
- Enter übernimmt niemals eine destruktive Standardaktion
- destruktive Aktionen benötigen ausgeschriebenen Anwendungs- oder Modulnamen
- Updatehinweis wird hervorgehoben, blockiert die normale Nutzung aber nicht
- erfolgreiche Aktionen enden mit Ergebnis und nächstem sinnvollen Schritt
- Fehler zeigen Fehlercode, betroffene Komponente und Diagnosebefehl
- Passwörter, Tokens und Dateiinhalte erscheinen nie im Diagnoseprotokoll
- Fensterbreite wird ab 80 Spalten vollständig dargestellt
- unter 80 Spalten wechselt die Ansicht in einzeilige Listeneinträge
- Farben ergänzen den Text, tragen aber nie allein Bedeutung
```


## Einmalige Host-Installation

```text
install.ps1   Windows
install.sh    Linux
install.sh    macOS
```

```text
Bootstrap starten
-> Host und CPU-Architektur erkennen
-> ausgewählte Format-Kompatibilitäten ausschließlich für den aktuellen Benutzer registrieren
-> Dateizuordnungen und Binärformat-Handler einrichten
-> benötigte User-Space-Runtimes installieren
-> Runtime-Dienst und Anwendungsindex aktivieren
-> Bootstrap entfernt seine temporären Installationsdateien und sich selbst nach erfolgreicher Installation
```

## Benutzerbereich und native Integration

```text
Installationsumfang
-> Runtime, Konfiguration und Anwendungszustände gehören ausschließlich dem aktuellen Benutzer
-> Dateizuordnungen werden für den aktuellen Benutzer registriert
-> Menüeinträge, Icons, MIME-/UTType-/ProgID-Zuordnungen und URL-Schemas werden für den aktuellen Benutzer registriert
-> notwendige Hintergrundkomponenten laufen als Benutzerprozesse oder Benutzer-Dienste
-> globale Betriebssystemkomponenten werden weder ersetzt noch verändert
-> Rechteerhöhung erfolgt nur für eine konkrete, vom Nutzer bestätigte Aktion
-> keine Auswirkungen auf andere Benutzerkonten
```

```text
Linux
~/.local/bin/cross-os-compat
~/.local/share/cross-os-application-compatibility/
~/.config/cross-os-application-compatibility/
~/.cache/cross-os-application-compatibility/
~/.local/share/applications/
~/.config/systemd/user/                 nur benötigte Benutzer-Units
~/.local/share/dbus-1/services/         nur benötigte Benutzer-Bus-Dienste

Windows
%LOCALAPPDATA%\CrossOSApplicationCompatibility\
HKCU\Software\Classes\             Dateitypen, ProgIDs, URL-Schemas
HKCU\Software\CrossOSApplicationCompatibility\
Autostart oder geplante Aufgabe ausschließlich im Benutzerkontext

macOS
~/Library/Application Support/Cross-OS Application Compatibility/
~/Library/Preferences/Cross-OS Application Compatibility/
~/Library/Caches/Cross-OS Application Compatibility/
~/Applications/
~/Library/LaunchAgents/                 nur benötigte Benutzer-Agenten
```

```text
Nahtlos für den Benutzer
-> Doppelklick und Öffnen-mit
-> Startmenü, App-Launcher, Dock und Desktop-Suche
-> native Dateiauswahl und Speichern-Dialoge
-> Zwischenablage und Drag-and-drop
-> Benachrichtigungen
-> Drucken
-> Audio, Kamera und Mikrofon nach Berechtigung
-> URL- und Dateitypzuordnungen
-> Standardbrowser und Standard-Mailclient
-> sichere Kommunikation mit Host-Anwendungen
-> kontrollierte Hintergrundprozesse
```

Die Integration ist host-nativ, aber nicht grenzenlos. Eine Anwendung erhält höchstens die Rechte, die sich aus ihren Quellrechten, den Ziel-OS-Rechten und ausdrücklichen Nutzerfreigaben ergeben.

Nach der Einrichtung ist keine manuelle Verarbeitung pro Datei erforderlich:

```text
Datei herunterladen
-> per Doppelklick, Shell oder Installer starten
-> Format-Handler erkennt Quelle automatisch
-> Runtime entscheidet selbstständig zwischen direkter Ausführung und Installation
-> Anwendung zeigt ihren eigenen Installer oder ihr eigenes Programmfenster
```

Das Quellprogramm bestimmt anhand seines normalen Verhaltens, ob es portabel läuft, Dateien installiert, Benutzerzustand erzeugt oder sich selbst deinstalliert. Das Kompatibilitätssystem stellt dafür nur die benötigte ABI-, Laufzeit-, Pfad- und Host-Integration bereit.

## Verwaltungsmenü

```text
Universal Application Compatibility Layer
Host: automatisch erkannt

Kompatibilität installieren
  [1] Windows        .exe / .msi
  [2] macOS          .app / .pkg
  [3] Debian         .deb
  [4] RPM Linux      .rpm
  [5] Flatpak        .flatpak
  [6] Snap           .snap

Installierte Kompatibilität
  Windows       installiert | 12 Anwendungen | 3 Datenreste
  macOS         nicht installiert
  Debian        installiert | 4 Anwendungen  | 0 Datenreste
  RPM Linux     nicht installiert
  Flatpak       installiert | 7 Anwendungen  | 1 Datenrest
  Snap          nicht installiert

Verwaltung
  [I] Kompatibilität installieren
  [R] Kompatibilität reinstallieren
  [U] Kompatibilität deinstallieren
  [A] erkannte Anwendungen anzeigen
  [D] verbliebene Anwendungsdaten verwalten
  [S] Status und Diagnose
  [Q] Beenden

Update-Status
  Neue Version verfügbar: vNEXT
  Aktion: Reinstallation mit vorhandener Benutzerkonfiguration
```

### Kompatibilität installieren

```text
Quellformat auswählen
-> Runtime-Modul auswählen
-> Host-Backend installieren
-> Format-Handler registrieren
-> Dateizuordnung registrieren
-> Runtime als aktiv markieren
```

Linux-Beispiel:

```text
Windows-Kompatibilität
-> PE/COFF- und MSI-Erkennung
-> EXE/MSI-Launcher
-> Windows User-Space ABI Runtime
-> optional x86-32-Unterstützung
-> optional x86/x86-64-Übersetzung auf ARM64
-> XDG-, Wayland/X11-, PipeWire-, CUPS- und Vulkan-Integration
```

## Update-Prüfung und Reinstallation

Beim Start der installierten Verwaltung wird ausschließlich die veröffentlichte Versionsdatei des Projekts abgerufen und mit der lokal installierten Version verglichen.

```text
Verwaltung starten
-> lokale Runtime-Version lesen
-> aktuelle veröffentlichte Version abrufen
-> Prüfsumme und Signatur der Versionsmetadaten prüfen
-> Versionen vergleichen
-> bei neuer Version im Hauptmenü Hinweis anzeigen
-> keine automatische Änderung durchführen
```

```text
Neue Version verfügbar
Installiert: <lokale-version>
Verfügbar:  <aktuelle-version>

[R] Jetzt reinstallieren
[L] Änderungsinformationen anzeigen
[B] Zurück
```

Ein Update ist technisch immer eine vollständige Reinstallation der ausgewählten Kompatibilitätsmodule. Es existiert kein separater Updatepfad.

```text
Reinstallation starten
-> unveränderliche Benutzerkonfiguration laden
-> installierte Quellformat-Module und Moduloptionen erfassen
-> aktuelle signierte Release-Dateien laden
-> Prüfsummen verifizieren
-> neue Runtime in ein separates Staging-Verzeichnis installieren
-> Konfiguration gegen die aktuelle Schema-Version migrieren
-> Ergebnis vollständig validieren
-> laufende Anwendungen prüfen
-> Runtime atomar auf die neue Version umschalten
-> dieselben Module anhand der Benutzerkonfiguration registrieren
-> Anwendungsindex und interne Anwendungsdaten unverändert übernehmen
-> Dateizuordnungen und Host-Integration reproduzierbar neu erzeugen
-> alte Runtime und Staging-Reste löschen
-> installierte Version aktualisieren
```

### Reinstallationsgarantie

```text
Bleibt 1:1 erhalten
-> aktivierte Format-Kompatibilitäten
-> Benutzerkonfiguration
-> Moduloptionen
-> Anwendungsindex
-> installierte und portable Anwendungen
-> interne Anwendungsdaten
-> Berechtigungen und freigegebene Benutzerpfade
-> normale Dokumente, Bilder, Videos und Exporte

Wird vollständig ersetzt
-> Runtime-Binärdateien
-> Loader und Format-Handler
-> Host-Backends
-> mitgelieferte Bibliotheken
-> Verwaltungsprogramm
-> erzeugte Benutzer-Dateizuordnungen und Startintegration
```

Die Konfiguration liegt getrennt von Runtime und Reinstallationsdateien. Eine Reinstallation schreibt zunächst nur in Staging. Bei fehlgeschlagener Prüfung bleibt die bisherige Runtime aktiv und unverändert. Erst nach erfolgreicher Validierung erfolgt der atomare Wechsel.

## Selbstbereinigung des Installers

```text
Erfolgreiche Erstinstallation oder Reinstallation
-> Installation validieren
-> heruntergeladene Archive löschen
-> Staging-Verzeichnis löschen
-> temporäre Protokolle auf das feste Diagnoseprotokoll reduzieren
-> Bootstrap-Skript löschen
```

```text
Linux/macOS
-> laufendes Shell-Skript kann seine Verzeichnisreferenz entfernen
-> Abschluss läuft aus dem bereits geöffneten Prozess weiter

Windows
-> PowerShell-Bootstrap startet nach erfolgreichem Abschluss einen kurzlebigen Löschprozess
-> Löschprozess wartet auf das Ende des Bootstrap-Prozesses
-> Bootstrap-Datei und temporäres Bootstrap-Verzeichnis werden gelöscht
-> Löschprozess beendet sich
```

Das installierte Verwaltungsprogramm ist nicht der Bootstrap. Es bleibt im Benutzerbereich installiert und stellt Reinstallation, Status, Anwendungsverwaltung und Deinstallation bereit.

## Anwendung starten oder installieren

```text
.exe/.msi/.app/.pkg/.deb/.rpm/.flatpak/.snap öffnen
-> registrierter Host-Handler
-> Format und Architektur erkennen
-> zugeordnete Runtime starten
-> Anwendung entscheidet über eigenen Ausführungs- oder Installationsablauf
-> alle Installationsänderungen im privaten Anwendungsbereich erfassen
-> native Verknüpfungen und Dateizuordnungen des Hosts erzeugen
-> Anwendung in den Index aufnehmen
```

Es gibt keinen vorgeschalteten „Datei konvertieren“-Dialog und keine manuelle Wahl eines Zielpakets pro Anwendung.

## Format-Zuordnung

Das Menü ordnet jedes fremde Quellformat einem Laufzeitverhalten auf dem aktuellen Host zu. Die Linux-Paketarten sind keine wählbaren Erscheinungsformen einer EXE, sondern eigenständige Quellformate mit jeweils eigener Paket- und Laufzeitlogik.

```text
Windows Host
  .exe/.msi                  nativ
  .app/.pkg                  macOS-ABI-Runtime
  .deb/.rpm/.flatpak/.snap   Linux-ABI-Runtime

Linux Host
  .exe/.msi                  Windows-ABI-Runtime
  .app/.pkg                  macOS-ABI-Runtime
  .deb/.rpm                  Linux-Paketadapter
  .flatpak/.snap             jeweilige Paket-Runtime oder Adapter

macOS Host
  .app/.pkg                  nativ
  .exe/.msi                  Windows-ABI-Runtime
  .deb/.rpm/.flatpak/.snap   Linux-ABI-Runtime
```

## Anwendungsindex

```json
{
  "application_id": "vendor.application",
  "display_name": "Application",
  "source_format": "exe",
  "runtime_id": "windows-abi",
  "install_state": "installed|portable|removed|orphaned-data",
  "entrypoints": [],
  "managed_paths": [],
  "user_data_paths": [],
  "host_document_paths": [],
  "integration_objects": [],
  "last_seen": "ISO-8601"
}
```

Erfassung:

```text
Runtime-Prozess startet
-> Prozessbaum einer Application-ID zuordnen
-> durch Installer erzeugte Dateien und Konfiguration erfassen
-> Startpunkte und Host-Verknüpfungen registrieren
-> installierten Zustand anhand von Manifest, Entrypoints und Uninstall-Metadaten bestimmen
-> zurückbleibende verwaltete Pfade als Datenreste markieren
```

Zählung im Menü:

```text
Anwendungen
-> installierte Anwendungen
-> portable, bereits gestartete Anwendungen mit verwaltetem Zustand
-> entfernte Anwendungen mit verbliebenen internen Daten

Nicht mitzählen
-> reine Downloads, die nie gestartet wurden
-> normale Dokumente, Bilder, Videos, Archive und Benutzerexporte
```

## Deinstallation einer Anwendung

```text
Anwendung deinstalliert sich selbst
-> Runtime erkennt entfernte Entrypoints und Deinstallationsereignis
-> Host-Verknüpfungen entfernen
-> Programmdateien als entfernt markieren
-> verbleibende interne Zustandsdaten erfassen
-> normale Nutzerdokumente niemals automatisch löschen
```

Falls kein funktionierender Uninstaller vorhanden ist:

```text
Verwaltungsmenü
-> erkannte Anwendungen
-> Anwendung auswählen
-> verwaltete Programmkomponenten entfernen
-> interne Zustandsdaten behalten oder separat löschen
```

## Verbliebene Anwendungsdaten

```text
Verwaltung
-> verbliebene Anwendungsdaten
-> Anwendung auswählen

Anwendung: Example App
Status: Programm nicht mehr installiert

Interne Daten:
  Einstellungen       12 MiB
  Cache              340 MiB
  lokale Datenbank    48 MiB
  Runtime-Registry     2 MiB

[N] nur Cache löschen
[A] alle internen Daten endgültig löschen
[B] behalten
```

Pflichtwarnung vor vollständigem Löschen:

```text
Alle internen Daten dieser Anwendung werden endgültig gelöscht.
Dazu können Einstellungen, lokale Datenbanken, Anmeldestatus,
Spielstände und Wiederaufnahmeinformationen gehören.

Bei einer späteren Neuinstallation startet die Anwendung ohne diesen Zustand.
Normale Dokumente, Bilder, Videos und andere Dateien in Ihren freigegebenen
Benutzerordnern werden nicht gelöscht.

Zum Fortfahren den angezeigten Anwendungsnamen eingeben.
```

Löschkette:

```text
Application-ID auswählen
-> Prozessaktivität prüfen
-> gespeicherte Pfade gegen verwaltetes Manifest validieren
-> Host-Dokumentpfade und externe Mounts ausschließen
-> interne Daten löschen
-> leere private Verzeichnisse entfernen
-> Index aktualisieren
-> Löschprotokoll ohne Dateiinhalte speichern
```

## Trennung von internen Daten und Nutzdaten

```text
Interne, verwaltete Daten
-> Runtime-Registry
-> Preferences
-> AppData-/Library-/XDG-Anwendungszustand
-> Cache
-> lokale interne Datenbanken
-> private Spielstände, sofern sie im internen App-Bereich liegen
-> durch das Kompatibilitätssystem verwaltbar

Nutzdaten
-> Dokumente
-> Bilder
-> Videos
-> Musik
-> Downloads
-> Desktop
-> vom Nutzer gewählte Ordner
-> exportierte Projekte
-> niemals Teil einer automatischen Bereinigung
```

Linux-Beispiel:

```text
~/.local/share/cross-os-application-compatibility/apps/<app-id>/state/       intern, löschbar
~/.cache/cross-os-application-compatibility/apps/<app-id>/                   intern, löschbar
~/Dokumente/                                   Nutzerdateien, ausgeschlossen
~/Bilder/                                      Nutzerdateien, ausgeschlossen
~/Downloads/                                   Nutzerdateien, ausgeschlossen
```

Freigegebene Benutzerordner werden über Portals oder explizite Pfadfreigaben eingebunden. Die Runtime speichert dafür nur Berechtigungs- und Zuordnungsmetadaten, niemals das Eigentum an den dort erzeugten Dateien.

## Kompatibilität deinstallieren

```text
Verwaltungsmenü
-> Kompatibilität deinstallieren
-> Quellformat auswählen
-> abhängige Anwendungen und Datenreste anzeigen
```

```text
Windows-Kompatibilität entfernen

Installierte Anwendungen: 12
Portable Anwendungen mit Zustand: 2
Verbliebene interne Daten: 3
Normale Nutzerdokumente: nicht betroffen

[1] Abbrechen
[2] Runtime entfernen, Anwendungen und interne Daten behalten
[3] Runtime und Programmkomponenten entfernen, interne Daten behalten
[4] Runtime, Programmkomponenten und alle internen Daten entfernen
```

Regeln:

```text
Option 2
-> Format-Handler deaktivieren
-> gemeinsame Runtime des Benutzers entfernen, soweit sie von keinem anderen aktivierten Formatmodul benötigt wird
-> App-Bereiche und Zustandsdaten behalten
-> spätere Neuinstallation der Kompatibilität kann sie wiederverwenden

Option 3
-> zusätzlich verwaltete Programmdateien und Host-Integration entfernen
-> interne Zustandsdaten behalten

Option 4
-> zusätzlich alle internen Anwendungszustände löschen
-> Nutzerdaten in freigegebenen Benutzerordnern strikt ausschließen
```

Vor Option 4 ist dieselbe eindeutige Löschwarnung pro betroffener Anwendung oder als zusammengefasste Liste erforderlich.

## Ziel

```text
Fremde Anwendung
  -> natives Startverhalten des Hosts
  -> natives Fenster im Desktop des Hosts
  -> hosttypische Menü-, Datei-, URL- und Benachrichtigungsintegration
  -> keine VM
  -> kein fremdes Root-Dateisystem im Host
  -> keine global sichtbaren C:\-, /Applications- oder Linux-Paketpfade
  -> anwendungsbezogener, isolierter Kompatibilitätsbereich
```

## Nicht-Ziel

```text
- keine statische 1:1-Konvertierung beliebiger Binärprogramme
- keine direkte Umwandlung von Win32, Cocoa oder Linux-ABI in ein Paketformat
- keine Ausführung fremder Kernel-Treiber
- keine Vermischung fremder Systempfade mit dem Host-Dateisystem
- keine Umgehung von Signatur, Notarisierung oder Paket-Richtlinien
```

## Vollständige Übersetzungsfläche

Nicht nur Pfade, sondern jede beobachtbare Betriebssystemgrenze wird durch ein versioniertes Subsystem übersetzt:

```text
Prozess und Thread
-> Erzeugung, Beendigung, Priorität, Job-/Prozessgruppen, Signale, Handles

Objekte und Synchronisation
-> Events, Mutexes, Semaphore, Waits, Shared Memory, atomare Operationen

IPC
-> Named Pipes, Unix Domain Sockets, COM/OLE, D-Bus, XPC, RPC, Mach Ports

Dienste und Hintergrundarbeit
-> Windows SCM-Dienst <-> systemd --user / LaunchAgent / Benutzerprozess
-> keine automatische Abbildung eines privilegierten Systemdiensts auf einen Benutzer-Dienst

Dateien und Speicher
-> Pfade, ACLs, Locks, Attribute, Alternate Data Streams, Symlinks, Case-Sensitivity, Memory Mapping

Identität und Autorisierung
-> Benutzer, Gruppen, SIDs, UID/GID, Access Tokens, Integritätsstufen, ACLs, Entitlements, Capabilities

Konfiguration
-> Registry <-> private Registry-Datenbank / Preferences / deklarative Host-Integration

Desktop und Shell
-> Fenster, Fokus, Tray, globale Menüs, Taskbar/Dock, Dateizuordnung, URL-Schemas, Drag-and-drop

Datenübergabe
-> Zwischenablageformate, MIME-Typen, UTTypes, Shell Data Objects, Datei-Handles

Netzwerk
-> Sockets, DNS, Proxy, Zertifikatsspeicher, Firewall-Kontext, lokale Discovery

Kryptografie und Identitäten
-> Zufall, Hashes, TLS, Zertifikate, Credential Store, Keychain/libsecret/Credential Manager

Audio und Medien
-> WASAPI/CoreAudio/PipeWire, Codecs, MIDI, Kamera, Mikrofon

Grafik und Compute
-> Direct3D/Direct2D/DirectWrite/OpenGL/Metal/Vulkan, Farbräume, GPU-Speicher, Shader

Druck und Scannen
-> WinSpool/CUPS/PrintCore, native Druckdialoge, Scanner-Backends

Geräte
-> HID, Gamecontroller, USB, Bluetooth, serielle Ports; ausschließlich über Host-Treiber und Broker

Barrierefreiheit und Eingabe
-> UI Automation/Accessibility/AT-SPI, IME, Tastaturlayout, Touch, Stift

Zeit und Umgebung
-> Zeitzonen, Locale, Kalender, Umgebungsvariablen, bekannte Ordner, Energiezustand

Systemintegration
-> Browser, Mailclient, URI-Handler, Dokumentvorschau, Benachrichtigungen, Suchindex

Installation
-> Installer-Aktionen, Komponenten, Abhängigkeiten, Reparatur, Deinstallation, Dateizuordnungen
```

## Kommunikations-Broker

```text
Fremde Anwendung
-> Source-API-Stub
-> Source-Objekt- und Rechteprüfung
-> kanonischer Broker-Request
-> Policy Engine
-> Host-Adapter
-> Ziel-OS-Dienst oder Zielanwendung
-> Antwort, Fehlercode, Handle und Ereignis zurückübersetzen
```

Der Broker übersetzt Semantik und nicht nur Funktionsnamen. Er verwaltet Objektlebenszeiten, Identitäten, Handles, Timeouts, Abbruch, Rückkanäle und Rechte. Fremde IPC-Namen werden pro Anwendung und Benutzer namespaced. Direkter Zugriff auf den vollständigen Session- oder System-Bus ist ausgeschlossen.

Beispiele:

```text
COM-Automation -> typisierter Broker -> D-Bus/XPC/Host-COM
Windows Named Pipe -> private Unix Socket / Named Pipe -> ACL-geprüfter Endpunkt
Windows Service Control -> Benutzer-Service-Adapter -> systemd --user / LaunchAgent
ShellExecute URL -> OpenURI-Broker -> Standardanwendung des Hosts
IFileDialog/NSSavePanel -> Dokumentportal -> ausgewählte Datei als begrenztes Handle
Credential Manager -> Secret-Broker -> libsecret / Keychain / Windows Credential Manager
```

Nicht jede Schnittstelle besitzt ein korrektes Äquivalent. Fehlt eine semantisch sichere Abbildung, wird der Aufruf mit einem quelltypischen Fehler abgelehnt. Es wird niemals stillschweigend auf einen mächtigeren Host-Zugriff erweitert.

## Rechteerhaltende Übersetzung

Grundregel:

```text
Effektive Zielberechtigung
= Quellberechtigung
∩ Berechtigung des aktuellen Zielbenutzers
∩ App-Policy
∩ explizite Nutzerfreigabe
∩ verfügbare sichere Host-Abbildung
```

```text
Deny bleibt Deny.
Fehlende Quellberechtigung erzeugt keine Zielberechtigung.
Nicht abbildbare Rechte werden verweigert.
Kindprozesse erben höchstens die bereinigte Policy ihres Elternprozesses.
Broker und Runtime besitzen keine pauschale Vollmacht im Namen der Anwendung.
```

### Rechteobjekt

```json
{
  "application_id": "vendor.application",
  "source_identity": {},
  "source_integrity": "untrusted|low|medium|high|system",
  "source_acl": [],
  "target_identity": {},
  "capabilities": [],
  "user_grants": [],
  "denials": [],
  "expires": null,
  "audit_id": "uuid"
}
```

### Sicherheitsabbildung

```text
Windows Access Token, SID, DACL, MIC
-> interne Capability-Menge mit Deny-Einträgen
-> Linux UID/GID, Namespace, seccomp, Landlock/Portal/Broker
-> macOS Sandbox, Entitlements, Security-Scoped Access und XPC-Broker

Linux UID/GID, Modusbits, ACL, Capabilities, Namespace
-> interne Capability-Menge mit Deny-Einträgen
-> Windows restricted token, ACL, Job Object, AppContainer-artige Begrenzung
-> macOS Sandboxprofil, Entitlements und Broker

macOS Entitlements, Sandbox Extensions, TCC, Keychain ACL
-> interne Capability-Menge mit Deny-Einträgen
-> Windows oder Linux Host-Policy und Broker
```

Eine exakte 1:1-Abbildung ist nicht immer möglich. Dann gilt die restriktivere Schnittmenge. Quell-`Administrator`, `root`, `SYSTEM`, Linux-Capabilities, macOS-Entitlements oder Installer-Elevation werden nie automatisch in Host-Administratorrechte übersetzt.

### Erhöhung

```text
Anwendung fordert privilegierte Aktion an
-> Quellruntime prüft, ob die Quellplattform dafür Erhöhung verlangen würde
-> Broker beschreibt konkrete Zielaktion
-> Ziel-OS zeigt seinen nativen Berechtigungsdialog
-> Freigabe gilt nur für diese Aktion und diesen Broker-Endpunkt
-> kein dauerhaftes Admin-Token für die Anwendung
-> Entscheidung wird protokolliert
```

### Zugriff auf Nutzerdaten

```text
Datei öffnen/speichern
-> nativer Host-Dialog oder vorhandene explizite Freigabe
-> Broker erteilt Zugriff nur auf ausgewählte Datei oder Verzeichnis
-> Zugriffstyp read/write/create wird begrenzt
-> Handle oder Bookmark wird der Anwendung zugeordnet
-> Berechtigung kann widerrufen werden
```

Die nahtlose Integration bleibt erhalten: Dialog, Speichern, Drucken, Benachrichtigung oder Öffnen einer URL wirken nativ. Die Anwendung erhält jedoch nur den minimalen Zugriff hinter der jeweiligen Aktion.

## Architektur

```text
┌─────────────────────────────────────────────────────────────┐
│  CLI / TUI / Dateizuordnung                                │
│  install.ps1 | install.sh | uacl                           │
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  1. Detector                                                │
│  Magic Bytes, Container, CPU-ISA, Bitness, ABI, Signatur   │
│  PE/COFF | MSI | Mach-O | APP | XAR/PKG | ELF | DEB | RPM  │
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  2. Package Extractor                                       │
│  extrahiert Payload und Metadaten, führt nichts ungeprüft aus│
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  3. Application Model                                       │
│  manifest.json + immutable payload + mutable app data       │
│  Entrypoints | Abhängigkeiten | Dienste | Dateitypen        │
│  Rechte | Registry/Preferences | Desktop-Metadaten          │
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  4. Runtime Selector                                        │
│  Quell-ABI + Host-OS + Guest-ISA + Host-ISA                │
└──────────────┬──────────────────────┬───────────────────────┘
               │                      │
     gleiche ISA/Bitness     andere ISA oder Bitness
               │                      │
               ▼                      ▼
      Native CPU-Ausführung   ISA-Ausführungsmodul
                              WoW64 / x86-32-Modus / DBT
               └──────────────┬───────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  5. Source-OS ABI Runtime                                   │
│  Windows: PE loader + Win32/NT DLLs + Registry + COM       │
│  macOS: Mach-O loader + dyld ABI + Framework-Adapter       │
│  Linux: ELF loader + glibc/musl + benötigte .so Runtime    │
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  6. Host Backend                                            │
│  Filesystem | Prozesse | Threads | IPC | Netzwerk | Zeit   │
│  Fenster | Eingabe | Audio | Druck | GPU | Geräte          │
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  7. Host Integration                                        │
│  Linux: XDG, portals, .desktop, MIME, systemd --user        │
│  Windows: Startmenü, Dateizuordnung, Registry, Services     │
│  macOS: .app Wrapper, LaunchServices, UTType, Keychain      │
└─────────────────────────────┬───────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────┐
│  8. Optionaler Packager                                     │
│  DEB | RPM | Flatpak | Snap | MSI | APP | PKG              │
└─────────────────────────────────────────────────────────────┘
```

## Korrekte Ausführungskette

```text
Datei öffnen
-> Typ über Magic Bytes und Struktur erkennen
-> Signatur und Integrität prüfen
-> Payload ohne Ausführung extrahieren
-> Installationsaktionen in ein neutrales Application Model übernehmen
-> Host, Quell-ABI und CPU-Architektur bestimmen
-> ISA-Pfad auswählen
-> Source-OS-Laufzeit initialisieren
-> privaten virtuellen Namensraum der Anwendung einhängen
-> Entrypoint laden
-> API/ABI-Aufrufe an das Host Backend weitergeben
-> Rückgabewerte, Fehlercodes, Events und Handles zurückübersetzen
-> Fenster über den nativen Display-Server des Hosts anzeigen
-> Desktop-Integration des Hosts verwenden
```

## Position der x86/x64-/ISA-Schicht

```text
Anwendungs-Instruktionen
-> ISA-Ausführungsmodul, nur wenn Host die Instruktionen nicht direkt ausführt
-> Source-OS ABI Runtime
-> Host Backend
-> Host Kernel
-> Hardware
```

```text
x86-32-Anwendung -> x86-64-Host:
CPU-Kompatibilitätsmodus + 32-Bit-ABI-Runtime; normalerweise keine x86->x64-Binärübersetzung

x86-64-Anwendung -> x86-32-Host:
nicht unterstützt

x86/x86-64-Anwendung -> ARM64-Host:
dynamische Binärübersetzung erforderlich

ARM64-Anwendung -> x86-64-Host:
dynamische Binärübersetzung erforderlich

gleiche ISA + gleiche Bitness:
keine ISA-Übersetzung; nur Loader-, ABI- und OS-API-Kompatibilität
```

> Die ISA-Schicht ist kein universeller Kernel-Treiber. Sie ist ein pro Prozess geladener Ausführungsdienst. Ein Kernel-Modul ist nur für klar begrenzte Host-Funktionen zulässig und darf keine fremden Kernel-Treiber direkt ausführen.

## Neutrales Application Model

```json
{
  "schema": "cross-os-application-compatibility.application/v1",
  "id": "vendor.application",
  "source": {
    "format": "pe|msi|macho|app|pkg|elf|deb|rpm|flatpak|snap",
    "os_abi": "windows|macos|linux",
    "architecture": "x86|x86_64|arm64"
  },
  "entrypoints": [],
  "files": [],
  "dependencies": [],
  "environment": {},
  "registry_or_preferences": [],
  "services": [],
  "file_associations": [],
  "permissions": [],
  "desktop": {},
  "runtime": {},
  "target_package": {}
}
```

## Dateisystem

```text
Host Linux:
~/.local/share/cross-os-application-compatibility/apps/<app-id>/
  payload/       unveränderliche Programmdateien
  runtime/       private Source-OS-Laufzeit
  state/         Registry, Preferences, Datenbanken
  data/          veränderliche Anwendungsdaten
  cache/         Cache
  mounts/        explizit freigegebene Host-Pfade
  logs/          Laufzeitprotokolle
```

```text
Windows-Pfad C:\Program Files\Vendor\App
-> nur innerhalb des App-Namensraums
-> niemals als C:-Struktur im Linux-Host sichtbar

macOS-Pfad /Applications/App.app
-> nur innerhalb des App-Namensraums
-> Host sieht ausschließlich den verwalteten Payload

Linux-Pfad /usr/lib/app
-> nur innerhalb des App-Namensraums
-> keine Dateien direkt nach /usr schreiben
```

Pfadzugriff:

```text
Quellpfad
-> Source-OS-Pfadnormalisierung
-> Mount-/Policy-Auflösung
-> sicherer Host-Pfad oder Portal
-> Host-Dateisystem
```

## Module

```text
core/
  detector/
  application-model/
  policy/
  runtime-selector/
  process-supervisor/

formats/
  pe/
  msi/
  macho/
  app/
  pkg/
  elf/
  deb/
  rpm/
  flatpak/
  snap/

runtimes/
  windows-abi/
  macos-abi/
  linux-abi/

isa/
  native/
  x86-32-on-x86-64/
  x86-on-arm64/
  arm64-on-x86-64/

backends/
  linux/
  windows/
  macos/

integration/
  xdg/
  windows-shell/
  launch-services/

packagers/
  deb/
  rpm/
  flatpak/
  snap/
  msi/
  app/
  pkg/

launcher/
  cross-os-compat
  install.ps1
  install.sh
```

## Format-Pipelines

### EXE/MSI auf Linux

```text
PE/MSI
-> PE/MSI Parser
-> privater Windows-Anwendungsbereich
-> PE Loader
-> Win32/NT API Runtime
-> optional WoW64 oder Cross-ISA-DBT
-> Linux Backend
-> Wayland/X11 + PipeWire + ALSA + CUPS + Vulkan
-> XDG Desktop Integration
-> optional DEB/RPM/Flatpak/Snap Wrapper
```

### APP/PKG auf Linux

```text
APP/PKG
-> Bundle/XAR Parser
-> Mach-O Loader
-> CPU-ISA-Prüfung
-> macOS ABI Runtime
-> Framework-Adapter für tatsächlich implementierte APIs
-> Linux Backend
-> XDG Desktop Integration
```

```text
Status: experimentell und nur für explizit implementierte Frameworks.
Nicht unterstützte Cocoa-, Metal-, Objective-C-/Swift-Runtime- oder System-Extension-Aufrufe führen zu einer klaren Inkompatibilitätsmeldung.
Apple-Frameworks werden nicht aus macOS kopiert oder mitverteilt.
```

### DEB/RPM/Flatpak/Snap auf Windows oder macOS

```text
Paket
-> Paketmetadaten und Payload extrahieren
-> ELF + dynamischen Loader erkennen
-> Linux User-Space ABI Runtime
-> optional Cross-ISA-DBT
-> Host Backend
-> native Desktop-Integration
-> optional MSI/APP/PKG Wrapper
```

```text
Post-install-, maintainer- und lifecycle-Skripte:
-> nie direkt auf dem Host ausführen
-> deklarativ übersetzen, wenn Regel vorhanden
-> sonst isoliert protokollieren und Installation abbrechen
```

## Treiberregeln

```text
User-Space-Anwendung
-> kompatibel über Runtime und Host Backend

Windows-Kernel-Treiber (.sys)
-> nicht direkt laden
-> nur dokumentierte Gerätefunktion über ein separates Host-Backend nachbilden

Linux-Kernel-Module (.ko)
-> nicht auf Windows/macOS laden

macOS Kernel/System Extensions
-> nicht auf Linux/Windows laden

USB/GPU/Audio/Netzwerk
-> Host-Treiber besitzt die Hardware
-> Anwendung erhält eine übersetzte User-Space-Schnittstelle
```

## Installationsmodell

```text
1. Bootstrap wird einmalig gestartet.
2. Gewählte Quellformat-Kompatibilitäten werden auf dem Host registriert.
3. Danach öffnet der Nutzer fremde Anwendungen direkt wie lokale Dateien.
4. Die fremde Anwendung entscheidet selbst über Installation oder portable Ausführung.
5. Alle fremden Systempfade existieren ausschließlich im privaten App-Namensraum.
6. Host-Ordner für Dokumente und Medien werden kontrolliert eingebunden.
7. Der Anwendungsindex verfolgt Programmzustand, Integration und interne Datenreste.
8. Der Bootstrap löscht nach erfolgreicher Einrichtung alle temporären Installationsdateien und sich selbst.
9. Die installierte Benutzerverwaltung bleibt für Status, Reinstallation, Bereinigung und Deinstallation erhalten.
10. Es werden keine für den aktuellen Benutzeren Installationen, Dienste oder Konfigurationen für andere Benutzer angelegt.
```

## Sicherheit

```text
- Parser und Installer ohne Root/Admin starten
- Extraktion gegen Path Traversal und Symlink Escape absichern
- Installationsskripte standardmäßig blockieren
- standardmäßig verweigern und nur semantisch erforderliche Host-Freigaben erteilen
- Netzwerk, Geräte, Zwischenablage und Host-Dateien einzeln erlauben
- Signaturen erhalten und Herkunft anzeigen
- keine automatische oder dauerhafte Rechteerhöhung; Erhöhung nur aktionsbezogen über den Host-Broker
- keine Übernahme fremder setuid-, Service- oder Treiberrechte
- reproduzierbare Runtime-Versionen
- SBOM für Runtime und erzeugte Pakete
```

## Technische Grenzen

```text
Kompatibel:
- User-Space-Anwendungen mit unterstützter API/ABI
- Programme mit nativer oder verfügbarer ISA-Ausführung
- Anwendungen ohne zwingende fremde Kernel-Treiber

Nicht allgemein kompatibel:
- Kernel-Treiber, Anti-Cheat-Kerneltreiber, Endpoint-Security-Treiber
- tief integrierte Shell-Erweiterungen
- nicht implementierte Cocoa-/Win32-/Linux-APIs
- Programme mit hart codierten Annahmen über Kernel, Init-System oder Geräte
- kopiergeschützte oder lizenzrechtlich nicht redistributierbare Systembibliotheken
```

## MVP-Reihenfolge

```text
Phase 1
Linux Host + EXE/MSI + x86_64/x86 + vorhandene Windows-API-Kompatibilitätsruntime

Phase 2
Linux Host + ELF aus DEB/RPM + isolierter Linux-Runtime-Bereich

Phase 3
Host-Integration + Flatpak-Paketerzeugung + Portals

Phase 4
Windows/macOS Host Backends für Linux-ELF-User-Space

Phase 5
begrenzte Mach-O-/APP-Unterstützung mit klar definierter API-Liste
```

## Referenzen

- Linux `binfmt_misc`: Erkennung fremder Binärformate und Übergabe an einen User-Space-Interpreter
- Flatpak: Sandbox-, Berechtigungs- und Portalmodell
- Wine: PE-Loader sowie Windows-API-/NT-API-Kompatibilität in User Space
- Apple: APP-/PKG-Verteilung, Signierung und Notarisierung
