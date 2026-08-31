---
tags: [skill, obsidian, projekt, token-effizienz]
created: 2026-01-01
status: active
---
# Skill: obsidian-project-split

Teilt eine zu groß gewordene Projektdatei in ein **Unterverzeichnis mit Hub-Datei** auf: Der Kern
(Status, Learnings, offene Punkte) bleibt schlank lesbar, die Historie wandert in Teildateien.
Inhalte werden dabei **1:1 verschoben, nie gekürzt oder umformuliert** — der Split verteilt, er
löscht nichts.

Kandidaten liefert [[obsidian-token-efficiency-analysis]]. Da Dateien verschoben werden:
**Durchführung nur nach Bestätigung.**

---

## Scope

- **Nur Projektdateien** unter `Projects/` — Pläne, Setups und Skills werden **nie** gesplittet (Vollständigkeits-Regeln)
- Richtwert: Kandidat ab ~12 KB, bzw. ab ~8 KB bei erkennbarem Mix aus aktuellem Stand und wachsender Historie

## Ziel-Struktur

```
Projects/<Bereich>/<PROJEKT>/
├── _<PROJEKT>.md            ← Hub: Status, offene Punkte, Learnings, Links auf Teildateien
├── <PROJEKT>_Historie.md    ← Timeline, alte Fehlerkorrekturen, abgeschlossene Phasen
└── <PROJEKT>_<Thema>.md     ← optional weitere Teildateien
```

- Hub nach den Konventionen aus [[obsidian-hub-anlegen]]: Frontmatter, Einleitung, 🔤-Hinweis, Teildateien alphabetisch
- Aufteilungs-Prinzip: **Heißes bleibt im Hub** (was man bei jeder Projektfrage braucht), **Kaltes wandert aus** (was nur historisch interessiert)

---

## Ablauf

### Schritt 1 — Vorschlag erstellen (read-only)

```
Gliederung der Kandidaten-Datei holen
→ Abschnitte klassifizieren: Kern (Status, offene Punkte, Learnings, Stammdaten)
  vs. Historie (Timeline, erledigte Korrekturen) vs. eigenständige Themen
→ Vorschlag ausgeben: welche Abschnitte in welche Teildatei, erwartete Größen
→ Auf Bestätigung warten
```

### Schritt 2 — Struktur anlegen (nach Bestätigung)

1. Unterverzeichnis + Hub `_<PROJEKT>.md` anlegen
2. Teildateien anlegen, Abschnitte **unverändert** hineinverschieben (Frontmatter je Teildatei setzen)
3. Hub verlinkt alle Teildateien; jede Teildatei verlinkt zurück auf den Hub

### Schritt 3 — Links migrieren

Die alte Datei verschwindet — bestehende Wikilinks dürfen nicht brechen:

```
Alle Dateien mit Links auf [[<PROJEKT>]] finden
Pro Quelldatei: [[<PROJEKT>]] → [[_<PROJEKT>|<PROJEKT>]]
  (Alias hält den Lesetext stabil; zeigt ein Link inhaltlich auf einen
   Historie-Abschnitt, stattdessen auf die passende Teildatei umbiegen)
  ⚠ In Markdown-TABELLEN den Alias-Pipe escapen: [[_<PROJEKT>\|<PROJEKT>]]
    — sonst zerreißt der Pipe die Tabellenspalten
Eltern-Hub: Zeile behält ihre Position, Link zeigt auf den neuen Hub
```

⚠️ **Auch die Dateien in `Maintenance/` durchsuchen — nach Klartext, nicht nur nach Wikilinks.** Ein
Report kann die alte Platzierung ausdrücklich als *richtig* beurteilen; bleibt der Satz stehen,
bestätigt der nächste Lauf die Fehlplatzierung erneut und der Umzug wird rückwärts begründet. Solche
Sätze stehen als Prosa da — ein Backlink-Array findet sie nicht. **Gilt für jedes Verschieben einer
Notiz**, auch für ein einzelnes `git mv`.

⚠️ **Der Backlink-Scan ist kein Beleg — der Beleg ist die Gegenprobe „bleibt irgendwo noch ein
nackter `[[<PROJEKT>]]`?"** Scan-Ausgaben zeigen **abgeschnittene Zeilen**; steckt der echte
Wikilink weiter rechts, sieht der Auszug nach Klartext aus und der Link bliebe stehen. Erst eine
eigene Suche über das nackte Linkmuster **nach** der Migration ist der Nachweis, nicht der Scan davor.

⚠️ **Eine selbstgebaute Link-Prüfung meldet falsch, wenn sie den escapten Pipe nicht abstreift** —
funktionierende Links werden dann als tot ausgegeben. Vor dem Handeln die Prüfung erst gegen einen
bekannt guten Link testen: ein negativer Befund ist zuerst ein Verdacht gegen die Messung.

### Schritt 4 — Alte Datei entfernen und verifizieren

```
□ Zeilen-Vollständigkeit: Inhalt aller Teildateien zusammen = alte Datei
  (nichts verloren, nichts doppelt) — mengenmäßig belegen, nicht am Diff
□ Keine Backlinks mehr auf die alte Datei → erst dann löschen
□ Kurzlauf broken-links/orphan-Logik über die beteiligten Dateien
□ Hub-Eintrag im Eltern-Hub korrekt
```

### Schritt 5 — Dokumentieren

Tageseintrag: was wurde gesplittet, warum, neue Struktur. Der nächste
[[obsidian-token-efficiency-analysis]]-Lauf bestätigt die Verbesserung.

---

## Regeln

- **Nur nach ausdrücklicher Bestätigung** — der Skill verschiebt und löscht Dateien
- Inhalte **1:1 verschieben** — kein Kürzen, kein Umformulieren, kein Weglassen (Kürzung ist Sache anderer Skills)
- Alte Datei erst löschen, wenn Links migriert und Vollständigkeit verifiziert sind
- Pläne, Setups, Skills: **nie** splitten
- Hub-Konventionen einhalten: 🔤-Hinweis, alphabetische Listen, Hub-Verlink-Pflicht im Eltern-Hub
- Umbau **vor** dem Tages-Commit durchführen — die Git-Historie der alten Datei bleibt als Sicherheitsnetz (`git log --follow`)

## Aufruf

> „Führe obsidian-project-split für [Projekt] aus"
> „Teile die Projektdatei [X] auf"
> „Setz die Split-Kandidaten aus dem token-efficiency-report um"
