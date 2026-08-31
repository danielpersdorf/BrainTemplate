---
tags: [daily, hub]
created: 2026-01-01
---
# Daily Context

Hub für alle täglichen Session-Einträge. Die Tagesdateien liegen in Monatsordnern
(`YYYY-MM/YYYY-MM-DD.md`), jeder Monat hat einen eigenen Monats-Hub (`_YYYY-MM.md`) mit der
Index-Tabelle seiner Tage.

> Neue Einträge immer in die jeweilige Tagesdatei schreiben, nicht hier. Die Highlight-Zeile des Tages gehört in den Monats-Hub. Bei Monatswechsel: neuen Ordner + Monats-Hub anlegen und hier oben verlinken.

> 📌 Aufbau der Tagesdateien, Trigger und Kürzungsregeln: [[obsidian-daily-note]] und [[obsidian-daily-redundancy-check]]. Kurz: kein Datums-Titel (der Dateiname ist der Titel), eine `#`-Überschrift pro Thema mit `##`-Unterpunkten, `---` nur zwischen den `#`, **keine Wikilinks** (Dateinamen als Klartext).

> 📅 Konvention: Der Index hier ist absteigend chronologisch sortiert — neuester Monat immer oben.

---

## Monate

| Monat | Hub |
|---|---|
| *(noch keiner — der erste Monatsordner entsteht mit dem ersten Tageseintrag)* | |

---

## Vorlage für einen Monats-Hub

Neuen Ordner `Daily/YYYY-MM/` anlegen und darin `_YYYY-MM.md`:

```markdown
---
tags: [daily, hub]
created: YYYY-MM-DD
---
# YYYY-MM

> Tagesübersicht für diesen Monat. Details stehen in den Tagesdateien, das Wissen in den Zieldateien.

> 📅 Konvention: absteigend chronologisch sortiert — neuester Tag oben.

> 🛑 Die Highlight-Zeile ist ein Stichwortverweis, keine Zusammenfassung: **Obergrenze 250 Zeichen, Zielkorridor 120–200**. Beim Ergänzen wird die ganze Zeile neu formuliert, nicht hinten etwas drangehängt — genau daran wächst sie.

| Tag | Highlights |
|---|---|
| [[YYYY-MM-DD]] | Projekt Stichwort; zweites Thema |
```

Danach die Zeile für den neuen Monat oben in die Tabelle dieser Datei eintragen.
