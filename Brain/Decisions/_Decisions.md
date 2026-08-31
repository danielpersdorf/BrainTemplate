---
tags: [decision, hub]
created: 2026-01-01
---
# Decisions — Hub

> Entscheidungen und verbindliche Regeln, je Projekt in einer eigenen Datei
> (`PROJEKTNAME_decisions.md`). Hier steht das **Warum**: Begründung, verworfene Alternative,
> Vorfall dahinter.

> 🔤 Konvention: Listen-Einträge in dieser Datei immer alphabetisch sortieren — neue Einträge an der korrekten Position einfügen, nicht unten anhängen.

> ⚠️ **Eine Regel hierher zu verlagern heißt nicht, sie aus dem Hub zu entfernen.** `Decisions/` wird nur auf Nachfrage gelesen, die Projekt-/Hub-Datei bei jedem Arbeiten. Die **Kurzfassung bleibt dort**, hierher wandert die Begründung. Prüfstein: *Könnte jemand allein mit dem Hub-Text die Regel korrekt befolgen?* Nein → zu kurz gekürzt.

## Dateien

| Datei | Inhalt |
|---|---|
| *(noch keine — die erste Decisions-Datei kommt hierher)* | |

## Aufbau einer Decisions-Datei

```markdown
---
tags: [decision]
created: YYYY-MM-DD
projekt: ProjektName
---
# ProjektName — Entscheidungen

## Thema (YYYY-MM-DD)

**Regel:** Was gilt — ein Satz, befolgbar.

**Warum:** Die Begründung.

**Verworfen:** Welche Alternative, und woran sie gescheitert ist.

**Anlass:** Der Vorfall oder die Messung dahinter.
```

Ein Fund außerhalb von `Decisions/` ist kein Fehler — [[obsidian-decision-scan]] meldet solche
Stellen, umgezogen wird nur nach Bestätigung.
