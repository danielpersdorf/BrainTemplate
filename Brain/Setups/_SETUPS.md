---
tags: [setup, hub]
created: 2026-01-01
---
# Setups

> Infrastruktur- und Einrichtungs-Dokumentation: wie etwas installiert, konfiguriert und am Laufen
> gehalten wird. Maschinen, Dienste, MCP-Server, Backup-Ketten, geplante Aufgaben.

> 🔤 Konvention: Listen-Einträge in dieser Datei immer alphabetisch sortieren — neue Einträge an der korrekten Position einfügen, nicht unten anhängen.

> 📄 **Setups werden nie gekürzt oder gesplittet.** Sie müssen self-contained sein — sie gehen an Menschen oder an fremde Agenten, die die übrigen Brain-Dateien **nicht** haben. Bewusste Redundanz ist hier ein Feature, kein Fehler.

## Einträge

| Setup | Inhalt |
|---|---|
| *(noch keiner)* | |

## Aufbau einer Setup-Datei

```markdown
---
tags: [setup]
created: YYYY-MM-DD
---
# Name des Setups

> Ein Satz: was wird hier eingerichtet, auf welcher Maschine.

## Voraussetzungen
## Installation
Schritt für Schritt, mit den echten Befehlen.

## Konfiguration
Wo liegen die Dateien, welche Werte sind zu setzen.

## Test
Woran erkennt man, dass es läuft.

## Fallstricke
Was beim ersten Mal schiefging und woran man es erkennt.
```
