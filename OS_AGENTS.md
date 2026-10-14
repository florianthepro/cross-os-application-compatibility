# OS_AGENTS.md

## Auftrag

OS-Agents implementieren COAC anhand von `README.md`. Sie bauen keine Dateikonverter und ersetzen weder Hostbetriebssystem noch Hosttreiber. Sie implementieren ausführbare Kompatibilitätsmodule, Resolver, Broker, Zustandsmodelle, Tests und Hostadapter unter `scr/`.

`README.md`, `AGENTS.md`, `OS_AGENTS.md`, `COAC_ASCII_ART.txt` und `TERMINAL_UI.txt` werden nicht verändert.

Bei fehlender oder widersprüchlicher Semantik fragen OS-Agents nach. Sie erfinden keine Betriebssystemsemantik und deklarieren keine unvalidierte Annahme als kompatibel.

## Verbindliche Laufzeitarchitektur

Ein Setup-Skript ist nur Bootstrap und Verwaltung. Die eigentliche Übersetzung erfolgt durch von ihm installierte ausführbare COAC-Komponenten.

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

Zulässige Komponenten:

```text
- kompilierte Loader und Runtime-Bibliotheken
- ausführbare Broker- und Verwaltungsprozesse
- pro Prozess geladene API-/ABI-Module
- dynamische Binärübersetzer bei abweichender ISA
- klar begrenzte Host-Bridges oder Hostmodule, wenn keine sichere User-Space-API existiert
- Setup-, Registrierungs-, Reinstallations- und Deinstallationsskripte
```

Nicht zulässig:

```text
- reine Dateiendungsumbenennung oder statische Paketkonvertierung als Kompatibilität
- Ersatz des Hostkernels, Grafik-Stacks oder nativer Hardwaretreiber
- ungeprüftes Ausführen fremder Installationsskripte auf dem Host
- Rechteausweitung gegenüber Quellanwendung, Hostbenutzer oder App-Policy
- Kopieren oder Verteilen nicht freigegebener Betriebssystemdateien
- pro Anwendung manuell erzeugte Sonderlösung als alleiniger Laufzeitpfad
```

## Aufteilung der Agents

Die Implementierung wird zweidimensional aufgeteilt:

```text
Quell-OS-Agent
-> implementiert die Semantik, die die fremde Anwendung erwartet

Ziel-OS-Agent
-> bildet kanonische COAC-Fähigkeiten auf native Host-APIs ab

Paar-Agent
-> validiert die konkrete Kombination Quell-OS -> Ziel-OS
```

Damit wird die Quellsemantik nicht für jedes Zielbetriebssystem erneut implementiert. Ebenso wird ein Hostadapter nicht für jede Quellanwendung neu gebaut.

```text
Windows-Quellmodul --+
macOS-Quellmodul  ----+-> kanonische COAC-Verträge -> Linux-Hostadapter
Linux-Quellmodul  ----+                             -> Windows-Hostadapter
                                                    -> macOS-Hostadapter
```

Ein Paar-Agent darf nur die Verbindung, Abweichungen und Tests seiner Kombination ergänzen. Gemeinsame Semantik gehört in das Quellmodul oder den kanonischen COAC-Vertrag, nicht in duplizierten Paar-Code.

## Quell-OS-Agents

### Windows Source Agent

Verantwortlich für:

```text
PE/COFF, EXE, DLL, MSI
API Sets, Export Forwarder, Ordinals, Delay Imports
LoadLibrary/GetProcAddress
NTDLL, Win32, COM/OLE, WinRT, RPC, Named Pipes
Registry, SCM, Access Tokens, SIDs, ACLs, MIC
Windows-Objekte, Handles, IOCP, Jobs, Waits und Callbacks
Direct3D, WASAPI, WinSpool, SetupAPI und Treiber-/IOCTL-Verträge
```

Arbeitsweise:

```text
öffentliche Spezifikation, Header und ABI erfassen
-> Binär-, Import- und Metadatenanalyse
-> Referenztests auf einer rechtmäßigen Windows-Installation
-> API-, IPC-, Zustands- und Nebenwirkungs-Traces
-> unabhängigen COAC-Vertrag und Implementierung erzeugen
-> Differentialtests gegen Windows
-> Abweichungen als Regressionstests speichern
```

System32, SysWOW64, WinSxS, API Sets, Registry, Dienste und Treiberverträge sind Analysequellen, aber keine pauschal kopierbaren COAC-Bestandteile.

### macOS Source Agent

Verantwortlich für:

```text
Mach-O, APP, PKG, dyld-Verträge und dylibs
Objective-C- und Swift-Runtime
Foundation, AppKit, CoreFoundation, CoreGraphics und QuartzCore
Metal, CoreAudio, AVFoundation, Security, Network und IOKit
XPC, Mach Ports, LaunchServices, UTType, Keychain
Entitlements, TCC, Sandbox Extensions und System Extensions
```

Arbeitsweise:

```text
Mach-O Load Commands, Exporte, Bindings und Runtime-Metadaten erfassen
-> Framework-, Klassen-, Selector- und Protokollverträge bestimmen
-> Referenztests auf einer rechtmäßigen macOS-Installation
-> Objektlebenszeit, Callbacks, Ereignisse und Nebenwirkungen messen
-> unabhängigen COAC-Vertrag und Implementierung erzeugen
-> Differentialtests gegen macOS
```

Proprietäre Apple-Framework-Binärdateien werden nicht mit COAC verteilt.

### Linux Source Agent

Verantwortlich für:

```text
ELF, PT_INTERP, Relocations, TLS, Symbolversionen und GNU IFUNC
Linux-psABI und direkte Systemaufrufe
glibc, musl, pthread, libdl, libstdc++ und Laufzeitbibliotheken
D-Bus, systemd, Wayland, X11, XDG und Desktop-Portals
PipeWire, ALSA, CUPS, udev und Geräteverträge
DEB, RPM, Flatpak, Snap und deren Lifecycle-Semantik
UID/GID, ACLs, Capabilities, Namespaces und seccomp-Verträge
```

Arbeitsweise:

```text
ELF-, Symbol-, Loader- und Paketmetadaten erfassen
-> Bibliotheks- und Syscall-Verträge bestimmen
-> Referenztests auf passenden Linux-Distributionen
-> IPC-, Service-, Desktop- und Paketwirkungen messen
-> unabhängigen COAC-Vertrag und Implementierung erzeugen
-> Differentialtests gegen Linux
```

Distributions- und Versionsunterschiede werden als explizite Vertragsvarianten modelliert, nicht stillschweigend vereinheitlicht.

## Ziel-OS-Agents

### Windows Host Agent

Implementiert kanonische COAC-Fähigkeiten über native Windows-Schnittstellen:

```text
Win32/NT, COM/WinRT, Windows Security, Credential Manager
Windows Shell, Dateizuordnungen und Benutzerintegration
Direct3D/DirectWrite, WASAPI, WinSpool und native Geräteverwaltung
Benutzerprozesse, geplante Aufgaben und erforderliche sichere Host-Bridges
```

Einstiegspunkt: `scr/windows/11/setup.ps1`

### macOS Host Agent

Implementiert kanonische COAC-Fähigkeiten über native macOS-Schnittstellen:

```text
AppKit, Foundation, LaunchServices, XPC und Keychain
Metal, CoreAudio, PrintCore und native Geräteverwaltung
LaunchAgents, TCC, Entitlements und Security-Scoped Access
```

Einstiegspunkt: `scr/macos/setup.sh`

### Linux Host Agent

Implementiert kanonische COAC-Fähigkeiten über native Linux-Schnittstellen:

```text
Wayland/X11, D-Bus, systemd --user und XDG-Portals
Vulkan/OpenGL, PipeWire/ALSA, CUPS und udev
UID/GID, ACLs, Namespaces, seccomp und verfügbare Sicherheitsmechanismen
Desktopdateien, MIME, URL-Schemas und Benutzerintegration
```

Einstiegspunkt: `scr/linux/setup.sh`

## Paar-Agents und Validierungsmatrix

Für jede nicht native Kombination existiert ein Paar-Agent als Test- und Integrationsverantwortlicher:

```text
Windows -> Linux
Windows -> macOS
macOS   -> Windows
macOS   -> Linux
Linux   -> Windows
Linux   -> macOS
```

Native Kombinationen dienen als Referenz:

```text
Windows -> Windows
macOS   -> macOS
Linux   -> Linux
```

Jeder Paar-Agent prüft mindestens:

```text
Loader und Start
Prozess-, Thread- und Objektverhalten
Dateien, Speicher und Rechte
IPC und Kommunikation mit Hostsoftware
Fenster, Eingabe und Desktopintegration
Netzwerk, Kryptografie und Credential Stores
Grafik, Audio, Druck und Geräteweitergabe
Installation, Reparatur, Deinstallation und Aufräumer
Fehlercodes, Callbacks, Ereignisreihenfolge und Rollback
```

## Contract Registry statt zentraler API-Datenbank

COAC benötigt keine einzelne manuell gepflegte Datenbank aller APIs. Quellmodule registrieren ausführbare Verträge selbst. Ein daraus erzeugter Index dient nur der Auflösung und kann jederzeit neu aufgebaut werden.

```text
angeforderte Bibliothek, Framework, Klasse, Selector, Symbol,
Syscall, IPC-Protokoll, CLSID oder IOCTL
-> Contract Resolver
-> selbstregistriertes Quellmodul
-> gemeinsame Objekt- und Rechteprüfung
-> passende kanonische COAC-Fähigkeit
-> Ziel-OS-Hostadapter
```

Ein Vertrag enthält mindestens:

```text
Quell-OS und Versionsbereich
Bibliothek/Framework/Protokoll
Symbol, Ordinal, Selector, Syscall oder Nachrichten-ID
Calling Convention und Datenlayout
Objekt- und Handlemodell
Rechte und Identität
Callbacks, Ereignisse und Nebenwirkungen
Fehler- und Abbruchverhalten
kanonische Hostfähigkeit
Differential- und Regressionstests
```

Der Contract Resolver darf einen unbekannten Vertrag nicht erraten. Er meldet einen stabilen COAC-Fehlercode und protokolliert die fehlende Schnittstelle ohne sensible Nutzdaten.

## Agenten-Workflow

```text
1. README.md und vorhandene Verträge lesen.
2. Quellvertrag und Zielhost bestimmen.
3. Öffentliche Spezifikationen, Header, offene Referenzimplementierungen und Binärmetadaten erfassen.
4. Rechtmäßige Referenztests für Normal-, Fehler-, Rechte- und Konkurrenzpfade erzeugen.
5. Beobachtbare Rückgaben, Callbacks, Nebenwirkungen und Zustandsänderungen erfassen.
6. Contract-IR und ausführbares Quellmodul implementieren.
7. Auf bestehende kanonische COAC-Fähigkeiten abbilden.
8. Fehlende Hostfähigkeit im zuständigen Ziel-OS-Adapter implementieren.
9. Gegen den nativen Referenzhost differenziell testen.
10. Sicherheits-, Rechte-, Installations-, Bereinigungs- und Rollbacktests ausführen.
11. Erst nach bestandenen Tests als unterstützt registrieren.
```

## Ablage unter scr

OS-Agents legen Implementierungen ausschließlich unter `scr/` ab. Empfohlene Struktur:

```text
scr/
  common/
    contracts/
    object-model/
    broker/
    policy/
    registry/
    tests/
  source/
    windows/
    macos/
    linux/
  host/
    windows/
    macos/
    linux/
  pairs/
    windows-to-linux/
    windows-to-macos/
    macos-to-windows/
    macos-to-linux/
    linux-to-windows/
    linux-to-macos/
  linux/setup.sh
  macos/setup.sh
  windows/11/setup.ps1
```

Die Setup-Skripte installieren ausschließlich die für den aktuellen Host und die Benutzerkonfiguration benötigten Komponenten. Sie enthalten nicht die gesamte Kompatibilitätslogik.

## Abnahmeregel

Eine Schnittstelle gilt erst als implementiert, wenn:

```text
- Loader und ABI korrekt sind,
- Normal- und Fehlerpfade gegen den Referenzhost geprüft wurden,
- Rechte nicht erweitert werden,
- Objektlebenszeit und Nebenwirkungen übereinstimmen,
- native Host-APIs und vorhandene Hosttreiber verwendet werden,
- Installation und Deinstallation im Objektmanifest nachvollziehbar sind,
- Regressionstests reproduzierbar bestehen,
- bekannte Abweichungen ausdrücklich dokumentiert sind.
```
